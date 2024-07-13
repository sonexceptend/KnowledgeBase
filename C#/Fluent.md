# Реализация fluent builder в C#

[link](https://nesteruk.wordpress.com/2010/08/25/fluent-builder-in-csharp/)

## Простой пример

Паттерн Builder в основном полезен для сложных объектов, но здесь и далее примеры будут упрощены, и начнем мы с самого тривиального. Допустим что у вас есть объект типа Person:

```c#
class Person
{
  public string Name { get; set; }
  public int Age { get; set; }
}
```

Чтобы добавить сборщик (PersonBuilder) для подобного объекта, сначала надо определить, куда его помещать. Тут есть несколько вариантов – мне на ум приходят как минимум четыре:

* Не делать отдельного сборщика, а заставить сам Person собирать себя, благо это можно реализовать. К сожалению, такой подход загрязняет API.
* Сделать отдельный сборщик как вложенный private класс.
* Сделать сборщик обычным отдельным internal классом (а можно даже public – особых рисков это не несет), а все подвластные ему структуры действительно сделать вложенными.
* Сделать сборщик классом-контейнером методов расширений (т.е. static), и сделать метод(ы) расширения для начала работы с конфигурацией.

Давайте возьмем 3й вариант. Представим на секунду, что объект Person имеет статический метод Create(), который возвращает некий PersonBuilder:

```c#
public class Person
{
  public string Name {get;set;}
  public int Age {get;set;}
  public static PersonBuilder Create() 
  {
    return new PersonBuilder(new Person());
  }
}
```

Итак, мы видим первый трюк fluent builder’ов: создаваемый объект пробрасывается через все вызовы. Это полезно. Только вот наблюдательный читатель задаст вопрос: какого-такого Create() возвращает PersonBuilder а не Person? Спокойствие, мой падаван, ответ уже близок.

Класс PersonBuilder – это обычный класс который держит ссылку на Person. Помимо этого, он дает нам возможность управлять создаваемым объектом через методы (или свойства – об этом скоро) с помощью fluent-вызовов, т.е. методов которые кончаются на return this. Но самой замечательной особенностью является то, что наш builder имеет оператор неявного приведения к Person. Вот как все это выглядит:

```c#
public class PersonBuilder
{
  private Person person;
  public PersonBuilder(Person person)
  {
    this.person = person;
  }
  public PersonBuilder Called(string name)
  {
    person.Name = name;
    return this;
  }
  public PersonBuilder Age(int age)
  {
    person.Age = age;
    return this;
  }
  public static implicit operator Person(PersonBuilder pb)
  {
    return pb.person;
  }
}
```

Использование этого сборщика тривиально:

```c#
Person me = Person.Create().Called("Dmitri").Age(25);
```

Итак, мы познакомились еще с одним правилом: любой конечный сборщик должен быть приводим к типу, который собирает. Под “конечным сборщиком” я подразумеваю не только PersonBuilder, но также любой специализированный сборщик, который PersonBuilder мог подставить. В следующей секции мы как раз об этом и поговорим.

## Промежуточные объекты

Если использовать наш сборщик для сложных объектов, мы перегрузим API настолько, что у пользователя голова закружится. Это особенно критично если сам по себе объект сложный, т.к. намного более полезным было бы предложить пользователю конфигурировать различные аспекты по отдельности.

Давайте сделаем еще один пример: у нас есть все тот же Person, и у него есть два набора свойств (или два аггрегированных объекта – без разницы) – адрес и информация о месте работы. Перепишем Person:

```c#
public class Person
{
  public string Name {get;set;}
  public int Age {get;set;}
   
  public string StreetAddress {get;set;}
  public string PostCode {get;set;}
  public string Country {get;set;}
   
  public string CompanyName {get;set;}
  public string Position {get;set;}
  public int AnnualIncome {get;set;}
   
  public static PersonBuilder Create() 
  {
    return new PersonBuilder(new Person());
  }
}
```

Для конфигурации адреса и работы отдельно создаются промежуточные сборщики (т.н. “подсборщики”), в нашем случае PersonAddressBuilder и PersonJobBuilder. Эти классы – обязательно вложенные. Возьмем первый класс – вот его базовое определение:

```c#
public class PersonAddressBuilder
{
  private Person person;
  public PersonAddressBuilder(Person person)
  {
    this.person = person;
  }
  public PersonAddressBuilder At(string streetAddress)
  {
    person.StreetAddress = streetAddress;
    return this;
  }
  public PersonAddressBuilder WithPostCode(string postCode)
  {
    person.PostCode = postCode;
    return this;
  }
  public PersonAddressBuilder In(string country)
  {
    person.Country = country;
    return this;
  }
}
```

Думаю намек понят – согласно правилу про которое я уже писал, Person пробрасывается и сюда. Пока что мы видим обычный fluent interface, но это только пока. Тем временем, PersonBuilder выбрасывает этот сборщик через свойство что, собственно, не запрещено:

```c#
public PersonAddressBuilder Lives
{
  get
  {
    return new PersonAddressBuilder(person);
  }
}
```

Вернемся к PersonAddressBuilder – как я уже писал, вы обязаны выбрасывать отсюда Person по желанию. Но помимо этого, вы должны дуплицировать все fluent-подвызовы корнегого builder’а в каждом подсборщике. От такого словоизвержения может начать глючить, поэтому вот сразу код:

```c#
public PersonJobBuilder Works
{
  get
  {
    return new PersonJobBuilder(person);
  }
}
public static implicit operator Person(PersonAddressBuilder builder)
{
  return builder.person;
}
```

Ну вот собственно и всё. А второй сборщик реализован симметрично:

```c#
public class PersonJobBuilder
{
  private Person person;
  public PersonJobBuilder(Person person)
  {
    this.person = person;
  }
  public PersonJobBuilder At(string companyName)
  {
    person.CompanyName = companyName;
    return this;
  }
  public PersonJobBuilder AsA(string position)
  {
    person.Position = position;
    return this;
  }
  public PersonJobBuilder Earning(int dollarsPerMonth)
  {
    person.AnnualIncome = dollarsPerMonth;
    return this;
  }
  public PersonAddressBuilder Lives
  {
    get
    {
      return new PersonAddressBuilder(person);
    }
  }
  public static implicit operator Person(PersonJobBuilder builder)
  {
    return builder.person;
  }
}
``` 

Мораль тут в том, что подсборщики внутренне позволяют пользователю переключаться между ними. Побочным эффектом является то, что они знают друг о друге, и неинформированность о новых подсборщиках может “сломать шаблон”. По-хорошему, для подобных целей нужно метапрограммирование.

Тем временем, у нас всё. Хотите посмотреть как это счастье использовать? Да вот так:

```c#
Person me = Person.Create()
  .Lives.At("123 London Road").WithPostCode("SO17 1BJ").In("Southampton")
  .Works.At("CRSI").AsA("VisitingResearcher").Earning(12345);
```

## Сложные сценарии

Сушествует несколько более сложных сценариев для использования fluent-конфигураторов (сборщиков, builder’ов, называйте как хотите).

Во-первых, вполне реальна ситуация, когда количество вложенных сборщиков больше двух. Это породжает целый пласт проблем, т.к. сборщики каждого уровня должны быть “переключаемы”, но также нужно иметь возможность переключаться “наверх”, а не только вниз.

Во-вторых, существуют ситуации когда хочется создавать свои собственный объекты исключительно для конфигурации. Например, что если я хочу указать возраст не только как 25 но как 25.years + 7.months? Это возможно, но для этого нужно много методов расширения, и в результате у нас получится загрязнение API, много промежуточных объектов, много переопределенных операторов. Слава богу что большинство их этого можно вынести в отдельные классы, которые не мешаются с основным “контентом”.

Ну и наконец, осталась нераскрытой тема использования анонимных классов и Expression<T> – но это относится скорее к типам параметров к типам параметров чем к инфраструктуре. А про инфраструктуру пока все. Comments welcome.
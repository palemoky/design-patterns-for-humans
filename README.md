![Design Patterns For Humans](https://cloud.githubusercontent.com/assets/11269635/23065273/1b7e5938-f515-11e6-8dd3-d0d58de6bb9a.png)

---
<p align="center">
🎉通俗讲解设计模式🎉
</p>

<p align="center">
设计模式是一个让许多人都难以理解的概念，在这篇文章中，我尽可能简单地去解释究竟什么是设计模式。
</p>

---

# 引言

设计模式用于解决那些经常发生的一些问题，它不是类、包或者库，在引入你的项目后就可以期待奇迹的发生。相反，它只是一本告诉你如何处理这些问题的手册。
> 设计模式是那些反复出现问题的解决方案，是一本告诉你如何处理这些问题的手册

维基百科这样解释
> 在软件工程中，软件设计模式是对软件设计中给定上下文中常见问题的一种通用的可重用解决方案，它不是可以直接转换为源代码或者机器码的成品设计，它只是一个描述或者模板，用于解决可以在许多不同情况下使用的问题。

## ⚠️注意

---

- 设计模式不是银弹
- 不要滥用设计模式，这可能会导致新的问题
- 设计模式是解决问题的方法，而不是查找问题的方法
- 如果设计模式被正确使用，它会给你带来巨大的收益，否则，你的代码只会越来越混乱

> 以下示例代码均采用 PHP 7，但这并不妨碍你理解设计模式。

## 设计模式的类型

---

- [创建型](#创建型设计模式)
- [结构型](#结构型设计模式)
- [行为型](#行为型设计模式)

# 创建型

白话版：
> 创建型设计模式的目标是如何实例化一个或一组相关的对象。

维基版：
> 在软件工程中，创建型设计模式是处理对象创建机制的设计模式，试图以适合情况的方式创建对象。对象创建的基本形式可能会导致设计问题或者增加设计的复杂性。创建性设计模式通过某种方式控制这个对象的创建来解决这个问题。

- [简单工厂模式](#🏠简单工厂)
- [工厂方法模式](#工厂方法)
- [抽象工厂模式](#抽象工厂)
- [生成器模式]()
- [原型模式]()
- [单例模式]()

## 🏠简单工厂(Simple Factory)
现实案例
> 想象一下，你打算建造一座房子，此时你需要定制一些门，你可以自己购置木头和所需工具来做这扇门，也可以打一个电话给工厂，告诉他你需要一扇什么样的门，然后等待送货即可。

白话版
> 简单工厂模式可为调用者生成一个实例，而无需暴露相关的逻辑。

维基版
> 在面向对象编程（OOP）中，工厂是用于创建其他对象的对象——形式上，工厂是一个函数或者方法，它从某个方法调用返回不同类型或类的对象。

### 编程示例
首先，我们创建一个门的接口和实现
```php
interface Door
{
    public function getWidth(): float;
    public function getHeight(): float;
}

class WoodenDoor implements Door
{
    protected $width;
    protected $height;

    public function __construct(float $width, float $height)
    {
        $this->width = $width;
        $this->height = $height;
    }

    public function getWidth(): float
    {
        return $this->width;
    }

    public function getHeight(): float
    {
        return $this->height;
    }
}
```

然后我们有一个工厂来生产门
```php
class DoorFactory
{
    public static function makeDoor($width, $height): Door
    {
        return new WoodenDoor($width, $height);
    }
}
```

之后我们就可以像这样使用
```php
// 制作一扇 100x200 的门
$door = DoorFactory::makeDoor(100, 200);

echo 'Width: ' . $door->getWidth();
echo 'Height: ' . $door->getHeight();

// 制作一扇 50x100 的门
$door2 = DoorFactory::makeDoor(50, 100);
```

**何时使用？**
当创建一个对象涉及到一些逻辑处理时，把它放在一个专用的工厂中，而不是到处重复相同的代码。

## 🏭工厂方法(Factory Method)
现实案例
> 想象一下招聘经理，他不可能面试每一个求职者，他会根据岗位或时间来决定是否委托其他同事去面试求职者。

白话版
> 工厂方法提供了一种将实例化委托给子类的方法。

维基版
> 在基于类的编程中，工厂方法模式是一种创建型的模式，它使用工厂方法来处理创建对象的问题，而不必指定将要创建对象的确切类。通过调用工厂方法，而不是通过调用构造方法来完成对象的创建。

### 编程示例
依然采用招聘经理的例子。我们首先创建一个面试者的接口与一些实现
```php
interface Interviewer
{
    public function askQuestions();
}

class Developer implements Interviewer
{
    public function askQuestions()
    {
        echo 'Asking about design patterns!';
    }
}

class CommunityExecutive implements Interviewer
{
    public function askQuestions()
    {
        echo 'Asking about community building';
    }
}
```

现在，我们创建面试官
```php
abstract class HiringManager
{
    // 工厂方法
    abstract protected function makeInterviewer(): Interviewer;

    public function takeInterview()
    {
        $interviewer = $this->makeInterviewer();
        $interviewer->askQuestions();
    }
}
```

现在，任何子类都可以继承并且创建面试官
```php
class DevelopmentManager extends HiringManager
{
    protected function makeInterviewer(): Interviewer
    {
        return new Developer();
    }
}

class MarketingManager extends HiringManager
{
    protected function makeInterviewer(): Interviewer
    {
        return new CommunityExecutive();
    }
}
```

之后，我们可以这样使用
```php
$devManager = new DevelopmentManager();
$devManager->takeInterview(); // Output: Asking about design patterns

$marketingManager = new MarketingManager();
$marketingManager->takeInterview(); // Output: Asking about community building.
```

** 何时使用？**
当类中有一些通用处理，但**所需的子类是在运行时动态决定**的，也就是说，当调用者不知道它可能需要什么确切的子类时，此时的工厂模式非常有用。

## 🔨 抽象工厂(Abstract Factory)
现实案例
> 我们仍以简单工厂中生产门为例。根据你的需要，你可以从木门店购买木门，从铁门店购买铁门，或者从某些商店购买聚氯乙烯门。另外，你可能需要不同专业的人来维修门，如木匠维修木门，铁匠维修铁门。

白话版
> 抽象工厂是简单工厂的工厂。它将单独但相关（依赖）的工厂分组在一起而不指定具体类别的工厂。

维基版
> 抽象工厂提供了一种将一组互相独立的工厂封装起来的方式，而无需指定它们具体的类。

### 编程示例
我们来将上边不同门的示例翻译为代码:
```php
interface Door
{
    public function getDescription();
}

class WoodenDoor implements Door
{
    public function getDescription()
    {
        echo 'I am a wooden door';
    }
}

class IronDoor implements Door
{
    public function getDescription()
    {
        echo 'I am an iron door';
    }
}
```

然后我们有了每种门对应的“专家”
```php
interface DoorFittingExpert
{
    public function getDescription();
}

class Welder implements DoorFittingExpert
{
    public function getDescription()
    {
        echo 'I can only fit iron doors';
    }
}

class Carpenter implements DoorFittingExpert
{
    public function getDescription()
    {
        echo 'I can only fit wooden doors';
    }
}
```

现在，抽象工厂能让我们创建一些相关对象，如木门工厂能创建木门和木匠，铁门工厂能创建铁门和铁匠
```php
interface DoorFactory
{
    public function makeDoor(): Door;
    public function makeFittingExpert(): DoorFittingExpert;
}

// 木门工厂将会返回木门与木匠
class WoodenDoorFactory implements DoorFactory
{
    public function makeDoor(): Door
    {
        return new WoodenDoor();
    }

    public function makeFittingExpert(): DoorFittingExpert
    {
        return new Carpenter();
    }
}

// 铁门工厂将会返回铁门与铁匠
class IronDoorFactory implements DoorFactory
{
    public function makeDoor(): Door
    {
        return new IronDoor();
    }

    public function makeFittingExpert(): DoorFittingExpert
    {
        return new Welder();
    }
}
```

让我们来使用一下这些工厂吧

```php
$woodenFactory = new WoodenDoorFactory();

$door = $woodenFactory->makeDoor();
$expert = $woodenFactory->makeFittingExpert();

$door->getDescription();  // Output: I am a wooden door
$expert->getDescription(); // Output: I can only fit wooden doors

// Same for Iron Factory
$ironFactory = new IronDoorFactory();

$door = $ironFactory->makeDoor();
$expert = $ironFactory->makeFittingExpert();

$door->getDescription();  // Output: I am an iron door
$expert->getDescription(); // Output: I can only fit iron doors
```

正如你所看到的，木门工厂封装了木匠和木门，铁门工厂封装了铁门和铁匠，这确保我们在创建木门时不会错误创建成铁匠。

**何时使用？**
当**存在相互关联的依赖关系**，但不涉及简单的创建逻辑时可以使用

## 👷 生成器模式(Builder)
现实案例
> 想象一下，你在哈迪百货购买了一件特别的东西，比如“大哈迪”，他们会直接把你订购的商品交付给你。这是一个简单工厂的例子，但在某些情况下，创建逻辑可能涉及多个步骤，比如，你要定制一份赛百味的套餐，你想要一份汉堡有很多的搭配选择，如，你想要哪种面包？哪种酱汁？什么奶酪？等。在这种情况下，生成器模式就派上用场了。

白话版
> 允许你创建不同形式的对象并避免构造函数污染。当创建多种形式的对象，或在创建过程中有许多步骤时，生成器模式非常有用。

[维基版](https://zh.wikipedia.org/wiki/%E7%94%9F%E6%88%90%E5%99%A8%E6%A8%A1%E5%BC%8F)
> 生成器模型是一种对象创建模式，其目的是解决弹性构造函数的反模式问题。

说到这里，我补充一下什么是弹性构造函数的反模式问题。我们都见过这样的构造函数：
```php
public function __construct($size, $cheese = true, $pepperoni = true, $tomato = false, $lettuce = true)
{
}
```

如你所见，构造函数的参数可能很快就会失控，而且你很难接参数的排列方式，另外，如果你想再添加更多的参数，参数列表会变得更长，这便是弹性构造函数的反模式问题。

### 编程示例
对于以上问题，明智的选择是使用生成器模式，首先我们有想要的汉堡
```php
class Burger
{
    protected $size;

    protected $cheese = false;
    protected $pepperoni = false;
    protected $lettuce = false;
    protected $tomato = false;

    public function __construct(BurgerBuilder $builder)
    {
        $this->size = $builder->size;
        $this->cheese = $builder->cheese;
        $this->pepperoni = $builder->pepperoni;
        $this->lettuce = $builder->lettuce;
        $this->tomato = $builder->tomato;
    }
}
```

接下来，我们需要生成器
```php
class BurgerBuilder
{
    public $size;

    public $cheese = false;
    public $pepperoni = false;
    public $lettuce = false;
    public $tomato = false;

    public function __construct(int $size)
    {
        $this->size = $size;
    }

    public function addPepperoni()
    {
        $this->pepperoni = true;
        return $this;
    }

    public function addLettuce()
    {
        $this->lettuce = true;
        return $this;
    }

    public function addCheese()
    {
        $this->cheese = true;
        return $this;
    }

    public function addTomato()
    {
        $this->tomato = true;
        return $this;
    }

    public function build(): Burger
    {
        return new Burger($this);
    }
}
```

最后，我们可以使用生成器来合理解决问题
```php
$burger = (new BurgerBuilder(14))
                ->addPepperoni()
                ->addLettuce()
                ->addTomato()
                ->build();
```

**何时使用？**
当对象有多种形式并避免构造函数参数过多时。
生成器模式与工厂模式的关键区别在于：当创建单步骤流程时使用工厂模式，而创建多步骤流程时则使用生成器模式。

## 🐑 原型模式（Prototype）
现实案例
> 还记得那只名叫“多莉”的绵羊吗？Bingo！就是第一只克隆羊。我们先不要过度关心这只羊是怎样产生的，只要知道是通过克隆产生的就可以了。

白话版
> 通过已存在的对象来创建新的对象

维基版
> 原型模式是软件开发中的一种创造性设计模式。当要创建的对象由一个原型实例确定时，将使用该实例进行克隆以生成新的对象。

简而言之，原型模式允许你创建一个现有对象的副本，并根据需要修改它，而不是从头创建一个对象。

### 编程示例
在`PHP`中，我们很容易通过`clone`关键字来实现
```php
class Sheep
{
    protected $name;
    protected $category;

    public function __construct(string $name, string $category = 'Mountain Sheep')
    {
        $this->name = $name;
        $this->category = $category;
    }

    public function setName(string $name)
    {
        $this->name = $name;
    }

    public function getName()
    {
        return $this->name;
    }

    public function setCategory(string $category)
    {
        $this->category = $category;
    }

    public function getCategory()
    {
        return $this->category;
    }
}
```

接下来，我们就可以像这样来克隆
```php
$original = new Sheep('Jolly');
echo $original->getName(); // Jolly
echo $original->getCategory(); // Mountain Sheep

// 克隆与修改所需内容
$cloned = clone $original;
$cloned->setName('Dolly');
echo $cloned->getName(); // Dolly
echo $cloned->getCategory(); // Mountain sheep
```

你也可以通过魔术方法`__clone`来修改克隆的行为。

**何时使用？**
当需要一个类似于现有对象的对象，或创建的开销大于克隆时，你便可以使用原型模式。

## 💍 单例模式（Singleton）
现实案例
> 一个国家在一个时期只能有一位总统。当需要行使权力时，只能由总统授权，总统便是单例。

白话版
> 确保特定类只会创建一个对象。

维基版
> 在软件工程中，单例模式是一种软件设计模式，它将类的实例化限制为一个对象。当需要一个对象来协调整个系统时，这非常有用。

单例模式实际上被认为是一种反模式，应该**避免过度使用**它。它不一定是坏的，可能有一些非常有效的场景，但应该谨慎使用，因为它在应用程序中**引入了一个全局状态**，在一个地方对其进行更改可能会影响到其他部分，并且非常难以调试。单例模式另一个缺点是，它使代码耦合性增大，并且模拟单例可能很难。

### 编程示例
**创建一个单例模式，需要私有化构造方法、禁用克隆、禁用继承，并且创建一个静态变量来存放实例。**
```php
final class President
{
    private static $instance;

    private function __construct()
    {
        // Hide the constructor
    }

    public static function getInstance(): President
    {
        if (!self::$instance) {
            self::$instance = new self();
        }

        return self::$instance;
    }

    private function __clone()
    {
        // Disable cloning
    }

    private function __wakeup()
    {
        // Disable unserialize
    }
}
```

接下来，让我们来使用单例模式
```php
$president1 = President::getInstance();
$president2 = President::getInstance();

var_dump($president1 === $president2); // true
```

# 结构型
白话版
> 结构型设计模式主要关注对象的结合，换言之，实例间如何相互调用。另一种解释是，它们有助于回答“如何构建软件组件”。

维基版
> 在软件工程中，结构设计模式是通过确定实现对象间关系的简单方式来简化设计的设计模式。

- 适配器（Adapter）
- 桥接（Bridge）
- 组合（Composite）
- 装饰器（Decorator）
- 外观（Facade）
- 享元（Flyweight）
- 代理（Proxy）

## 🔌 适配器
现实案例
> 想象一下，你有一些照片存储在内存卡上，你想传到电脑上。为了将照片传到电脑上，你需要一些兼容电脑端口的适配器，以使你可以将内存卡连接到电脑上。在这里，读卡器就是适配器。另一个常见的例子就是电源适配器，一个三插的插头无法直接插到两插的插座，我们需要一个转接头才能实现。最后的一个例子是，翻译可以使两个讲不通语言的人进行交流。

白话版
> 适配器模型可以将不兼容的对象包装起来，以使其与另一个类兼容。

维基版
> 在软件工程中，适配器模式是一种软件设计模式，它允许将现有类的接口用作另一个接口。它通常用于使现有类在不修改源代码的情况下与其他类协作。

### 编程示例
假设有一个猎人在狩猎狮子。
首先，我们有一个`Lion`接口。
```php
interface Lion
{
    public function roar();
}

class AfricanLion implements Lion
{
    public function roar()
    {
    }
}

class AsianLion implements Lion
{
    public function roar()
    {
    }
}
```

而猎人希望所有实现了`Lion`接口的狮子都能被捕获。
```php
class Hunter
{
    public function hunt(Lion $lion)
    {
        $lion->roar();
    }
}
```

现在，我们在狩猎中添加一只野狗，这样猎人也可以捕猎野狗。但我们不能直接添加，因为野狗有不同的接口。为了使野狗能被猎人捕获，我们必须创建一个兼容的适配器。
```php
class WildDog
{
    public function bark()
    {
    }
}

class WildDogAdapter implements Lion
{
    protected $dog;

    public function __construct(WildDog $dog)
    {
        $this->dog = $dog;
    }

    public function roar()
    {
        $this->dog->bark();
    }
}
```

现在，`WildDog`就可以使用`WildDogAdapter`了
```php
$wildDog = new WildDog();
$wildDogAdapter = new WildDogAdapter($wildDog);

$hunter = new Hunter();
$hunter->hunt($wildDogAdapter);
```

## 🚡 桥接模式（Bridge）
现实案例
> 假设你有一个网站，此时你打算支持用户修改页面主题，你会怎么做呢？为每个主题的每个页面创建多份副本？还是只创建一个主题，根据用户的设定来加载它们？桥接模式允许你以第二种方式实现。

![without bridge](https://cloud.githubusercontent.com/assets/11269635/23065293/33b7aea0-f515-11e6-983f-98823c9845ee.png)

![with bridge](https://cloud.githubusercontent.com/assets/11269635/23065293/33b7aea0-f515-11e6-983f-98823c9845ee.png)

白话版
> 桥接模式更倾向于组合而非继承，实现细节从层次结构推到另一个具有独立层次结构的对象。

维基版
> 桥接模式是软件工程中使用的一种设计模式，其目的是“把事物对象和其具体行为、具体特征分离开来，使它们可以各自独立的变化”。

### 编程示例
继续我们上述的网页主题例子，我们先将页面独立
```php
interface WebPage
{
    public function __construct(Theme $theme);
    public function getContent();
}

class About implements WebPage
{
    protected $theme;

    public function __construct(Theme $theme)
    {
        $this->theme = $theme;
    }

    public function getContent()
    {
        return "About page in " . $this->theme->getColor();
    }
}

class Careers implements WebPage
{
    protected $theme;

    public function __construct(Theme $theme)
    {
        $this->theme = $theme;
    }

    public function getContent()
    {
        return "Careers page in " . $this->theme->getColor();
    }
}
```

再独立主题
```php
interface Theme
{
    public function getColor();
}

class DarkTheme implements Theme
{
    public function getColor()
    {
        return 'Dark Black';
    }
}
class LightTheme implements Theme
{
    public function getColor()
    {
        return 'Off white';
    }
}
class AquaTheme implements Theme
{
    public function getColor()
    {
        return 'Light blue';
    }
}
```

实现页面变更主题
```php
$darkTheme = new DarkTheme();

$about = new About($darkTheme);
$careers = new Careers($darkTheme);

echo $about->getContent(); // "About page in Dark Black";
echo $careers->getContent(); // "Careers page in Dark Black";
```

## 🌿 组合模式
⚠️ 此处争议较大 [Wrong Composite Pattern Definition #28
](https://github.com/kamranahmedse/design-patterns-for-humans/issues/28)
现实案例
> 每个公司都是由员工组成的，每个员工都有相同的特点，即有工资，有一些职责，向某个领导汇报，有或没有下属等。

白话版
> 组合模式允许客户端以统一的方式处理单个对象。

维基版
> 在软件工程中，组合模式是一种分区设计模式。组合模式描述的是将一组对象看做单个对象相同的处理方式。组合的目的是将对象“组合”到树结构中去，以表示部分与整体的层次结构，实现组合模式允许客户端同一处理单个对象与组合。

### 编程示例
继续上述公司与员工的例子，此处我们有不同的员工类型
```php
interface Employee
{
    public function __construct(string $name, float $salary);
    public function getName(): string;
    public function setSalary(float $salary);
    public function getSalary(): float;
    public function getRoles(): array;
}

class Developer implements Employee
{
    protected $salary;
    protected $name;
    protected $roles;
    
    public function __construct(string $name, float $salary)
    {
        $this->name = $name;
        $this->salary = $salary;
    }

    public function getName(): string
    {
        return $this->name;
    }

    public function setSalary(float $salary)
    {
        $this->salary = $salary;
    }

    public function getSalary(): float
    {
        return $this->salary;
    }

    public function getRoles(): array
    {
        return $this->roles;
    }
}

class Designer implements Employee
{
    protected $salary;
    protected $name;
    protected $roles;

    public function __construct(string $name, float $salary)
    {
        $this->name = $name;
        $this->salary = $salary;
    }

    public function getName(): string
    {
        return $this->name;
    }

    public function setSalary(float $salary)
    {
        $this->salary = $salary;
    }

    public function getSalary(): float
    {
        return $this->salary;
    }

    public function getRoles(): array
    {
        return $this->roles;
    }
}
```

然后，我们就有了由不同员工组成的组织（公司）
```php
class Organization
{
    protected $employees;

    public function addEmployee(Employee $employee)
    {
        $this->employees[] = $employee;
    }

    public function getNetSalaries(): float
    {
        $netSalary = 0;

        foreach ($this->employees as $employee) {
            $netSalary += $employee->getSalary();
        }

        return $netSalary;
    }
}
```

最后，我们可以这样使用
```php
// 创建员工
$john = new Developer('John Doe', 12000);
$jane = new Designer('Jane Doe', 15000);

// 将员工加入公司
$organization = new Organization();
$organization->addEmployee($john);
$organization->addEmployee($jane);

echo "Net salaries: " . $organization->getNetSalaries(); // Net Salaries: 27000
```

## ☕ 装饰器
现实案例
> 假设你有一家汽车服务店，当顾客离店时，你会怎样计算账单呢？你将每项服务的价格累加，直到计算出总价。这里每种类型的服务都是装饰器。

白话版
> 装饰器模式允许你在运行时通过将对象包装在装饰器类的对象中动态更改对象的行为。

维基版
> 在 OOP 中，装饰器模式是一种设计模式，它允许将行为静态或动态地添加到单个对象中，而不影响来自同一类的其他对象的行为。装饰器模式通常有助于保持单一职责原则，因为它可以将功能划分为具有独立关注的部分。

### 编程示例
我们以咖啡为例，首先，我们有一个从`Coffee`接口实现的`SimpleCoffee`
```php
interface Coffee
{
    public function getCost();
    public function getDescription();
}

class SimpleCoffee implements Coffee
{
    public function getCost()
    {
        return 10;
    }

    public function getDescription()
    {
        return 'Simple coffee';
    }
}
```

我们希望是代码可扩展，以便在需要时可以修改它。让我们来做一些组件（装饰器）
```php
class MilkCoffee implements Coffee
{
    protected $coffee;

    public function __construct(Coffee $coffee)
    {
        $this->coffee = $coffee;
    }

    public function getCost()
    {
        return $this->coffee->getCost() + 2;
    }

    public function getDescription()
    {
        return $this->coffee->getDescription() . ', milk';
    }
}

class WhipCoffee implements Coffee
{
    protected $coffee;

    public function __construct(Coffee $coffee)
    {
        $this->coffee = $coffee;
    }

    public function getCost()
    {
        return $this->coffee->getCost() + 5;
    }

    public function getDescription()
    {
        return $this->coffee->getDescription() . ', whip';
    }
}

class VanillaCoffee implements Coffee
{
    protected $coffee;

    public function __construct(Coffee $coffee)
    {
        $this->coffee = $coffee;
    }

    public function getCost()
    {
        return $this->coffee->getCost() + 3;
    }

    public function getDescription()
    {
        return $this->coffee->getDescription() . ', vanilla';
    }
}
```

现在，让我们来冲一杯咖啡吧
```php
$someCoffee = new SimpleCoffee();
echo $someCoffee->getCost(); // 10
echo $someCoffee->getDescription(); // Simple Coffee

$someCoffee = new MilkCoffee($someCoffee);
echo $someCoffee->getCost(); // 12
echo $someCoffee->getDescription(); // Simple Coffee, milk

$someCoffee = new WhipCoffee($someCoffee);
echo $someCoffee->getCost(); // 17
echo $someCoffee->getDescription(); // Simple Coffee, milk, whip

$someCoffee = new VanillaCoffee($someCoffee);
echo $someCoffee->getCost(); // 20
echo $someCoffee->getDescription(); // Simple Coffee, milk, whip, vanilla
```

## 📦 外观模式
现实案例
> 你如何开启电脑？“当然是按下电源键了”，你回答道。事实确实如此，你通过电脑给所提供给外部的一个按钮，即可完成内部一系列的启动操作。这个复杂子系统的简单接口就是一个门面(Facade)。

白话版
> 外观模式为复杂子系统提供了一个简化的接口。

维基版
> 外观模式是一个对象，它为更大的代码体（如类库）提供了一个简化的接口。

### 编程示例
继续以上述的计算机为例，此处我们有一个`Computer`类
```php
class Computer
{
    public function getElectricShock()
    {
        echo "Ouch!";
    }

    public function makeSound()
    {
        echo "Beep beep!";
    }

    public function showLoadingScreen()
    {
        echo "Loading..";
    }

    public function bam()
    {
        echo "Ready to be used!";
    }

    public function closeEverything()
    {
        echo "Bup bup bup buzzzz!";
    }

    public function sooth()
    {
        echo "Zzzzz";
    }

    public function pullCurrent()
    {
        echo "Haaah!";
    }
}
```

然后我们有一个门面
```php
class ComputerFacade
{
    protected $computer;

    public function __construct(Computer $computer)
    {
        $this->computer = $computer;
    }

    public function turnOn()
    {
        $this->computer->getElectricShock();
        $this->computer->makeSound();
        $this->computer->showLoadingScreen();
        $this->computer->bam();
    }

    public function turnOff()
    {
        $this->computer->closeEverything();
        $this->computer->pullCurrent();
        $this->computer->sooth();
    }
}
```

现在，我们就可以使用这个门面了
```php
$computer = new ComputerFacade(new Computer());
$computer->turnOn(); // Ouch! Beep beep! Loading.. Ready to be used!
$computer->turnOff(); // Bup bup buzzz! Haah! Zzzzz
```

## 🍃 享元模式
现实案例
> 你喝过茶吗？通常我们不会只泡一杯茶，我们会泡几杯茶与朋友共饮。享元模式就是关于共享的。

白话版
> 它通过尽可能多地与相似对象共享来最小化内存或计算开销。

维基版
> 在计算机编程中，享元模式是一种软件设计模式。享元模式是通过与其他相似对象共享尽可能多的数据来最小化内存使用的对象。

### 编程示例
仍以上述的茶为例，首先，我们有不同的茶和茶壶
```php
// 任何被缓存的都是享元
// 此处的茶都是享元
class KarakTea
{
}

// 充当工厂并节省茶叶
class TeaMaker
{
    protected $availableTea = [];

    public function make($preference)
    {
        if (empty($this->availableTea[$preference])) {
            $this->availableTea[$preference] = new KarakTea();
        }

        return $this->availableTea[$preference];
    }
}
```

然后，客人就可以在茶馆享用泡好的茶了
```php
class TeaShop
{
    protected $orders;
    protected $teaMaker;

    public function __construct(TeaMaker $teaMaker)
    {
        $this->teaMaker = $teaMaker;
    }

    public function takeOrder(string $teaType, int $table)
    {
        $this->orders[$table] = $this->teaMaker->make($teaType);
    }

    public function serve()
    {
        foreach ($this->orders as $table => $tea) {
            echo "Serving tea to table# " . $table;
        }
    }
}
```

最后，我们可以这样使用
```php
$teaMaker = new TeaMaker();
$shop = new TeaShop($teaMaker);

$shop->takeOrder('less sugar', 1);
$shop->takeOrder('more milk', 2);
$shop->takeOrder('without sugar', 5);

$shop->serve();
// Serving tea to table# 1
// Serving tea to table# 2
// Serving tea to table# 5
```

## 🎱 代理模式
现实案例
> 你用过门禁卡吗？通过一扇门有多种方式，比如门禁卡、开门按钮、熟人带领等。门的主要功能是连接两个隔离的空间，并拦截一些东西。但我们可以在普通的门上添加代理来添加一些新的功能，如报警及防御功能等。如果你仍然不太明白，那就让我用代码来更好地解释一下。

白话版
> 使用代理模式，一个类可以展现出另一个类的功能。

维基版
> 代理，在其最一般的形式中，是一个作为其他接口的类。代理是客户端调用的包装器或代理对象，用于访问后台的真实服务对象。使用代理可以简单地转发，也可以提供额外的逻辑，如当对象上的操作是资源密集型时进行缓存，或者在调用对象前检查先决条件等。

### 编程示例
首先，我们有一个门的接口并实现它
```php
interface Door
{
    public function open();
    public function close();
}

class LabDoor implements Door
{
    public function open()
    {
        echo "Opening lab door";
    }

    public function close()
    {
        echo "Closing the lab door";
    }
}
```

然后我们有一个代理来认证通过门的人
```php
class SecuredDoor
{
    protected $door;

    public function __construct(Door $door)
    {
        $this->door = $door;
    }

    public function open($password)
    {
        if ($this->authenticate($password)) {
            $this->door->open();
        } else {
            echo "Big no! It ain't possible.";
        }
    }

    public function authenticate($password)
    {
        return $password === '$ecr@t';
    }

    public function close()
    {
        $this->door->close();
    }
}
```

最后，我们可以这样来使用
```php
$door = new SecuredDoor(new LabDoor());
$door->open('invalid'); // Big no! It ain't possible.

$door->open('$ecr@t'); // Opening lab door
$door->close(); // Closing lab door
```

另一个例子是某种数据映射器实现。例如，我最近使用这个模式为MongoDB创建了一个ODM(对象数据映射器)，在这个模式中，我使用魔术方法`_call()`围绕mongo类编写了一个代理。所有方法调用都代理到原始mongo类，检索到的结果按原样返回，但如果`find`或`findOne`数据映射到所需的类对象，则返回该对象而不是`Cursor`。

## 行为型
白话版
> 该模式表示对象间的职责分配。它们与结构模式的不同之处在于，它们不仅指定了结构，而且还概述了它们之间用于消息传递与通信的模式。换言之，这有助于回答“如何在软件组中实现行为？”

维基版
> 在软体工程中，行为型模式为设计模式的一种类型，用来识别对象间的常用交流模式并加以实现。这样，可在进行这些交流活动时增强弹性。

- 责任链模式(Chain of Responsibility)
- 命令模式(Command)
- 迭代器模式(Iterator)
- 中介者模式(Mediator)
- 备忘录模式(Memento)
- 观察者模式(Observer)
- 访问者模式(Visitor)
- 策略模式(Strategy)
- 状态模式(State)
- 模板方法模式(Template Method)

## 🔗 责任链模式
现实案例
> 比如你的支付宝账户设置了三种支付方式：花呗、余额、银行卡，且每种支付方式都有不同的额度。花呗有 100 元，余额有 300 元，银行卡有 1000 元，这三种支付方式的支付顺序为 花呗 ==> 余额 ==> 银行卡。当你想要购买一件价格为 210 元的商品时，此时的支付过程即为责任链模式：系统首先校验花呗是否足够支付，如果不足则验证余额，最后验证银行卡。

白话版
> 责任链模式有助于建立一个对象链。请求从一端进入，并从一个对象进入另一个对象，直到找到合适的处理程序为止。

维基版
> 责任链模式在 OOP 中是一种软件设计模式，它包含了一些命令对象和一系列的处理对象。每一个处理对象决定它能处理哪些命令对象，它也直到如何将它不能处理的命令对象传递给该链中的下一个处理对象。

### 编程示例
将例子翻译为代码。首先，我们创建一个账户
```php
abstract class Account
{
    protected $successor;
    protected $balance;

    public function setNext(Account $account)
    {
        $this->successor = $account;
    }

    public function pay(float $amountToPay)
    {
        if ($this->canPay($amountToPay)) {
            echo sprintf('Paid %s using %s' . PHP_EOL, $amountToPay, get_called_class());
        } elseif ($this->successor) {
            echo sprintf('Cannot pay using %s. Proceeding ..' . PHP_EOL, get_called_class());
            $this->successor->pay($amountToPay);
        } else {
            throw new Exception('None of the accounts have enough balance');
        }
    }

    public function canPay($amount): bool
    {
        return $this->balance >= $amount;
    }
}

class Bank extends Account
{
    protected $balance;

    public function __construct(float $balance)
    {
        $this->balance = $balance;
    }
}

class Paypal extends Account
{
    protected $balance;

    public function __construct(float $balance)
    {
        $this->balance = $balance;
    }
}

class Bitcoin extends Account
{
    protected $balance;

    public function __construct(float $balance)
    {
        $this->balance = $balance;
    }
}
```

现在，我们来设定支付顺序
```php
// 我们设定支付顺序为：$bank->$paypal->$bitcoin

$bank = new Bank(100);          // Bank with balance 100
$paypal = new Paypal(200);      // Paypal with balance 200
$bitcoin = new Bitcoin(300);    // Bitcoin with balance 300

$bank->setNext($paypal);
$paypal->setNext($bitcoin);

$bank->pay(259);

// 输出结果为
// ==============
// Cannot pay using bank. Proceeding ..
// Cannot pay using paypal. Proceeding ..:
// Paid 259 using Bitcoin!
```

## 👮 命令模式
现实案例
> 一个非常常见的例子是你在餐厅点餐的过程。你（客户端）将你需要的菜品告知服务员（调用者），服务员再转告给后厨（接收者），最终由后厨来完成你所需要的菜品。另外的例子还有你（客户端）通过遥控器（调用者）来控制电视（接收者）。

白话版
> 此模式允许你将操作封装到对象中，其背后的关键思想是将调用这与接收者进行解耦。

维基版
> 在 OOP 编程中，命令模式是一种设计模式，它尝试以对象来代替实际行动。命令对象可以把行动及其参数封装起来，于是这些行动可以被取消、重复多次、取消后又再重做。

### 编程示例
首先，我们创建一个接收者，它实现了所有可以执行的操作
```php
// Receiver
class Bulb
{
    public function turnOn()
    {
        echo "Bulb has been lit";
    }

    public function turnOff()
    {
        echo "Darkness!";
    }
}
```

然后，我们创建一个接口，并在实现这个接口的类中设定一些命令
```php
interface Command
{
    public function execute();
    public function undo();
    public function redo();
}

// Command
class TurnOn implements Command
{
    protected $bulb;

    public function __construct(Bulb $bulb)
    {
        $this->bulb = $bulb;
    }

    public function execute()
    {
        $this->bulb->turnOn();
    }

    public function undo()
    {
        $this->bulb->turnOff();
    }

    public function redo()
    {
        $this->execute();
    }
}

class TurnOff implements Command
{
    protected $bulb;

    public function __construct(Bulb $bulb)
    {
        $this->bulb = $bulb;
    }

    public function execute()
    {
        $this->bulb->turnOff();
    }

    public function undo()
    {
        $this->bulb->turnOn();
    }

    public function redo()
    {
        $this->execute();
    }
}
```

然后，我们通过 `Invoker` 来实现客户端与应用程序的交互
```php
// Invoker
class RemoteControl
{
    public function submit(Command $command)
    {
        $command->execute();
    }
}
```

最后，让我们看看如何在客户机中使用它
```php
$bulb = new Bulb();

$turnOn = new TurnOn($bulb);
$turnOff = new TurnOff($bulb);

$remote = new RemoteControl();
$remote->submit($turnOn); // Bulb has been lit!
$remote->submit($turnOff); // Darkness!
```

命令模式还可以用于实现基于事务的系统。它会将所有执行的命令记录下来，成功提交，失败回滚。

## ➿ 迭代器
现实案例
> 收音机就是一个很好的迭代器的案例。我们可以从某个频道开始，通过旋钮来遍历所有的节目。同样地，MP3 播放器与电视也可以通过上一曲/下一曲（上一个节目/下一个节目）来遍历所有的歌曲（节目）。换言之，它们都提供了一个接口来遍历各个频道、歌曲或节目。

白话版
> 迭代器提供了一种无需关心底层而可以访问对象元素的方法。

维基版
> 在 OOP 中，迭代器是一种设计模式，用于遍历容器并访问容器中的元素。迭代器模式将算法与容器解耦，但在某些情况下，因算法依赖于某些容器而无法解耦。

### 编程示例
在 PHP 中，通过 SPL(Standard PHP Library，PHP 标准库)非常容易实现迭代器。将上述例子翻译为代码。首先，我们创建一个 `RadioStation` 类
```php
class RadioStation
{
    protected $frequency;

    public function __construct(float $frequency)
    {
        $this->frequency = $frequency;
    }

    public function getFrequency(): float
    {
        return $this->frequency;
    }
}
```

接下来，我们创建一个迭代器
```php
use Countable;
use Iterator;

class StationList implements Countable, Iterator
{
    /** @var RadioStation[] $stations */
    protected $stations = [];

    /** @var int $counter */
    protected $counter;

    public function addStation(RadioStation $station)
    {
        $this->stations[] = $station;
    }

    public function removeStation(RadioStation $toRemove)
    {
        $toRemoveFrequency = $toRemove->getFrequency();
        $this->stations = array_filter($this->stations, function (RadioStation $station) use ($toRemoveFrequency) {
            return $station->getFrequency() !== $toRemoveFrequency;
        });
    }

    public function count(): int
    {
        return count($this->stations);
    }

    public function current(): RadioStation
    {
        return $this->stations[$this->counter];
    }

    public function key()
    {
        return $this->counter;
    }

    public function next()
    {
        $this->counter++;
    }

    public function rewind()
    {
        $this->counter = 0;
    }

    public function valid(): bool
    {
        return isset($this->stations[$this->counter]);
    }
}
```

最后，我们就可以用来遍历元素了
```php
$stationList = new StationList();

$stationList->addStation(new RadioStation(89));
$stationList->addStation(new RadioStation(101));
$stationList->addStation(new RadioStation(102));
$stationList->addStation(new RadioStation(103.2));

foreach($stationList as $station) {
    echo $station->getFrequency() . PHP_EOL;
}

$stationList->removeStation(new RadioStation(89)); // Will remove station 89
```

## 👽 中介者模式
现实案例
> 当你用手机与好友聊天时，消息并不是直接被好友接收到的，而是经过网络服务商的转发处理。这里，网络服务商即为中介者。

白话版
> 在中介者模式中，对象间的通信过程被封装在一个中介者（调解人）对象之中。 对象之间不再直接交互，而是通过调解人进行交互。 这么做可以减少可交互对象间的依赖，从而降低耦合。<sup>1</sup>

维基版
> 在软件工程领域，中介者模式定义了一个中介者对象，该对象封装了系统中对象间的交互方式。由于它可以在运行时改变程序的行为，这种模式是一种行为型模式 。<sup>2</sup>

### 编程示例
这里有一个简单的聊天室的例子，首先，我们创建一个中介者（聊天室）
```php
interface ChatRoomMediator 
{
    public function showMessage(User $user, string $message);
}

// Mediator
class ChatRoom implements ChatRoomMediator
{
    public function showMessage(User $user, string $message)
    {
        $time = date('M d, y H:i');
        $sender = $user->getName();

        echo $time . '[' . $sender . ']:' . $message;
    }
}
```

然后，我们需要创建用户
```php
class User {
    protected $name;
    protected $chatMediator;

    public function __construct(string $name, ChatRoomMediator $chatMediator) {
        $this->name = $name;
        $this->chatMediator = $chatMediator;
    }

    public function getName() {
        return $this->name;
    }

    public function send($message) {
        $this->chatMediator->showMessage($this, $message);
    }
}
```

最后，我们就可以聊天了
```php
$mediator = new ChatRoom();

$john = new User('John Doe', $mediator);
$jane = new User('Jane Doe', $mediator);

$john->send('Hi there!');
$jane->send('Hey!');

// 最终将输出
// Feb 14, 10:58 [John]: Hi there!
// Feb 14, 10:58 [Jane]: Hey!
```

## 💾 备忘录模式
现实案例
> 以 Word 为例，当你在编辑文本时，Word 会记录下你最近的一些操作，以便你在需要撤销到某个之前的状态可以很方便的实现。

白话版
> 备忘录模式是一种可以捕获与存储当前状态，并在之后可以恢复的方式。

维基版
> 备忘录模式是一种可以提供储存与恢复对象的软件设计。

备忘录模式通常在你需要提供撤销操作时非常有用。

### 编程示例
仍以 Word 编辑为例，首先，我们创建一个备忘录对象来储存状态
```php
class EditorMemento
{
    protected $content;

    public function __construct(string $content)
    {
        $this->content = $content;
    }

    public function getContent()
    {
        return $this->content;
    }
}
```

接下来，我们创建一个编辑器，它将用备忘录来储存状态
```php
class Editor
{
    protected $content = '';

    public function type(string $words)
    {
        $this->content = $this->content . ' ' . $words;
    }

    public function getContent()
    {
        return $this->content;
    }

    public function save()
    {
        return new EditorMemento($this->content);
    }

    public function restore(EditorMemento $memento)
    {
        $this->content = $memento->getContent();
    }
}
```

最后，我们就可以开始编辑了
```php
$editor = new Editor();

$editor->type('This is the first sentence.');
$editor->type('This is second.');

// 将以下内容保存: This is the first sentence. This is second.
$saved = $editor->save();

$editor->type('And this is third.');

// 输出最近一次的保存结果
echo $editor->getContent(); // This is the first sentence. This is second. And this is third.

// 恢复最近一次的保存结果
$editor->restore($saved);

$editor->getContent(); // This is the first sentence. This is second.
```

## 😎 观察者模式
现实案例
> 

白话版
> 

维基版
> 

### 编程示例

## 🏃 访问者模式
现实案例
> 想象一下去迪拜旅游的人，他门只需要办好签证，就可以在迪拜畅游无阻。你只需要告诉他们地址，他们就可以去尽情游玩。同样的，访问者模式允许你添加要访问的地点，从而使你参观更多的地方，而无需做额外的工作。

白话版
> 访问者模式允许你不做任何的变动就可以将进一步的操作添加到对象中。

维基版
> 在 OOP 中，访问者模式是一种将算法从其操作的对象结构中分离出来的方法。这种分离可以在不修改现有对象的情况下对其添加新的啊哦做。这遵循了开闭原则。

### 编程示例
以动物园为例，我们让不同的动物发出不同的声音
```php
// Visitee
interface Animal
{
    public function accept(AnimalOperation $operation);
}

// Visitor
interface AnimalOperation
{
    public function visitMonkey(Monkey $monkey);
    public function visitLion(Lion $lion);
    public function visitDolphin(Dolphin $dolphin);
}
```

接下来，我们需要创建具体的动物
```php
class Monkey implements Animal
{
    public function shout()
    {
        echo 'Ooh oo aa aa!';
    }

    public function accept(AnimalOperation $operation)
    {
        $operation->visitMonkey($this);
    }
}

class Lion implements Animal
{
    public function roar()
    {
        echo 'Roaaar!';
    }

    public function accept(AnimalOperation $operation)
    {
        $operation->visitLion($this);
    }
}

class Dolphin implements Animal
{
    public function speak()
    {
        echo 'Tuut tuttu tuutt!';
    }

    public function accept(AnimalOperation $operation)
    {
        $operation->visitDolphin($this);
    }
}
```

现在，让我们来实现访客
```php
class Speak implements AnimalOperation
{
    public function visitMonkey(Monkey $monkey)
    {
        $monkey->shout();
    }

    public function visitLion(Lion $lion)
    {
        $lion->roar();
    }

    public function visitDolphin(Dolphin $dolphin)
    {
        $dolphin->speak();
    }
}
```

最后，我们可以这样来使用
```php
$monkey = new Monkey();
$lion = new Lion();
$dolphin = new Dolphin();

$speak = new Speak();

$monkey->accept($speak);    // Ooh oo aa aa!    
$lion->accept($speak);      // Roaaar!
$dolphin->accept($speak);   // Tuut tutt tuutt!
```

我们可以简单地为动物创建一个继承层次结构来实现这一点，但当我们必须向动物添加新的功能是，就必须修改动物。有了访问者模式，我们就不需要需改动物来实现了，比如，我们现在向动物新增跳跃的能力
```php
class Jump implements AnimalOperation
{
    public function visitMonkey(Monkey $monkey)
    {
        echo 'Jumped 20 feet high! on to the tree!';
    }

    public function visitLion(Lion $lion)
    {
        echo 'Jumped 7 feet! Back on the ground!';
    }

    public function visitDolphin(Dolphin $dolphin)
    {
        echo 'Walked on water a little and disappeared';
    }
}
```

我们可以这样来使用
```php
$jump = new Jump();

$monkey->accept($speak);   // Ooh oo aa aa!
$monkey->accept($jump);    // Jumped 20 feet high! on to the tree!

$lion->accept($speak);     // Roaaar!
$lion->accept($jump);      // Jumped 7 feet! Back on the ground!

$dolphin->accept($speak);  // Tuut tutt tuutt!
$dolphin->accept($jump);   // Walked on water a little and disappeared
```

## 💡 策略模式
现实案例
> 我们现在需要对一组数据进行排序。我们可以通过最简单的冒泡排序来实现，但随着数据的增多，排序变得非常慢，为了解决这个问题，我们实现了快速排序。现在，我们又遇到了新的问题：虽然快排在大数据集时很快，但小数据集却很慢。为了解决这个问题，我们实现了一种策略，即小数据集用冒泡排序，大数据集用快速排序。

白话版
> 策略模式允许你根据情况切换算法或策略。

维基版
> 在计算机编程中，策略模式是一种行为型模式，它允许在运行时选择算法。

### 编程示例
将排序的例子翻译为代码。首先，我们创建一个策略接口以及不同策略的实现
```php
interface SortStrategy
{
    public function sort(array $dataset): array;
}

class BubbleSortStrategy implements SortStrategy
{
    public function sort(array $dataset): array
    {
        echo "Sorting using bubble sort";

        // Do sorting
        return $dataset;
    }
}

class QuickSortStrategy implements SortStrategy
{
    public function sort(array $dataset): array
    {
        echo "Sorting using quick sort";

        // Do sorting
        return $dataset;
    }
}
```

然后，我们的调用者就可以在不同的情况下使用不同的策略
```php
class Sorter
{
    protected $sorter;

    public function __construct(SortStrategy $sorter)
    {
        $this->sorter = $sorter;
    }

    public function sort(array $dataset): array
    {
        return $this->sorter->sort($dataset);
    }
}
```

最后，我们可以这样来使用
```php
$dataset = [1, 5, 4, 3, 2, 8];

$sorter = new Sorter(new BubbleSortStrategy());
$sorter->sort($dataset); // Output : Sorting using bubble sort

$sorter = new Sorter(new QuickSortStrategy());
$sorter->sort($dataset); // Output : Sorting using quick sort
```

## 💢状态模式
现实案例
> 假设你是一位画家，当你使用画笔来作画的时候，你用了红色的颜料，就会描绘出红色的线条，你用了蓝色的颜料时，就会描绘出蓝色的线条。

白话版
> 状态模式可以实现类的行为随着状态的不同而变化。

维基版
> 状态模式是一种行为型设计模式，它以 OOP 的方式实现状态机。在状态模式中，通过将每个单独状态实现为状态模式接口的一个继承类，而状态间的转变通过调用在模式的父类中定义的函数来实现，从而实现一个状态机。状态模式可以解释为一种策略模式，它能通过调用在模式接口中定义的方法来切换当前策略。

### 编程示例
以 Word 为例，当你为选中的字体进行相应的字体和颜色设置时，被选中文本就会发生相应的变化。
首先，我们创建一个状态接口和实现的类
```php
interface WritingState
{
    public function write(string $words);
}

class UpperCase implements WritingState
{
    public function write(string $words)
    {
        echo strtoupper($words);
    }
}

class LowerCase implements WritingState
{
    public function write(string $words)
    {
        echo strtolower($words);
    }
}

class DefaultText implements WritingState
{
    public function write(string $words)
    {
        echo $words;
    }
}
```

然后，我们创建一个编辑器
```php
class TextEditor
{
    protected $state;

    public function __construct(WritingState $state)
    {
        $this->state = $state;
    }

    public function setState(WritingState $state)
    {
        $this->state = $state;
    }

    public function type(string $words)
    {
        $this->state->write($words);
    }
}
```

最后，我们可以这样来使用
```php
$editor = new TextEditor(new DefaultText());

$editor->type('First line');

$editor->setState(new UpperCase());

$editor->type('Second line');
$editor->type('Third line');

$editor->setState(new LowerCase());

$editor->type('Fourth line');
$editor->type('Fifth line');

// Output:
// First line
// SECOND LINE
// THIRD LINE
// fourth line
// fifth line
```

## 📒 模板方法模式
现实案例
> 假设我们需要建造一所房子，我们需要：
> - 打地基
> - 砌墙
> - 盖屋顶
> - 加盖其他层
> 这些步骤我们无法变更，比如你无法在砌完墙之前盖屋顶，但有些步骤我们可以做一些小的修改，如，砌墙的材料可以用木头、水泥或石头

白话版
> 模板方法定义了如何执行特定算法的框架，但这些步骤由子类实现。

维基版
> 在软件工程中，模板方法模式是一种行为设计模式，它定义了操作中算法的程序框架，将一些步骤推迟到子类。它允许在不改变算法结构的情况下重新定义算法的某些步骤。

### 编程示例
假设我们有一个构建工具帮助我们测试，如代码检查、构建、生成报告等。
首先，我们创建一个为构建算法而指定的框架的基类。
```php
abstract class Builder
{

    // Template method
    final public function build()
    {
        $this->test();
        $this->lint();
        $this->assemble();
        $this->deploy();
    }

    abstract public function test();
    abstract public function lint();
    abstract public function assemble();
    abstract public function deploy();
}
```

然后，我们需要实现基类
```php
class AndroidBuilder extends Builder
{
    public function test()
    {
        echo 'Running android tests';
    }

    public function lint()
    {
        echo 'Linting the android code';
    }

    public function assemble()
    {
        echo 'Assembling the android build';
    }

    public function deploy()
    {
        echo 'Deploying android build to server';
    }
}

class IosBuilder extends Builder
{
    public function test()
    {
        echo 'Running ios tests';
    }

    public function lint()
    {
        echo 'Linting the ios code';
    }

    public function assemble()
    {
        echo 'Assembling the ios build';
    }

    public function deploy()
    {
        echo 'Deploying ios build to server';
    }
}
```

最后，我们可以这样来使用
```php
$androidBuilder = new AndroidBuilder();
$androidBuilder->build();

// Output:
// Running android tests
// Linting the android code
// Assembling the android build
// Deploying android build to server

$iosBuilder = new IosBuilder();
$iosBuilder->build();

// Output:
// Running ios tests
// Linting the ios code
// Assembling the ios build
// Deploying ios build to server
```


---
# 总览
| 模式名称 | 描述 |
| - | - |
|**创建型**| |
|简单工厂模式||
|工厂方法模式模式|定义一个接口用于创建对象，但是让子类决定初始化哪个类。工厂方法把一个类的初始化下放到子类|
|抽象工厂|为一个产品族提供了统一的创建接口。当需要这个产品族的某一系列的时候，可以从抽象工厂中选出相应的系列创建一个具体的工厂类。|
|生成器模式|将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。|
|原型模式|用原型实例指定创建对象的种类，并且通过拷贝这些原型,创建新的对象。|
|单例模式|确保一个类只有一个实例，并提供对该实例的全局访问。|
|**结构型**||
|适配器模式|将某个类的接口转换成客户端期望的另一个接口表示。适配器模式可以消除由于接口不匹配所造成的类兼容性问题。|
|桥接模式|将一个抽象与实现解耦，以便两者可以独立的变化。|
|组合模式|把多个对象组成树状结构来表示局部与整体，这样用户可以一样的对待单个对象和对象的组合。|
|装饰器模式|向某个对象动态地添加更多的功能。修饰模式是除类继承外另一种扩展功能的方法。|
|外观模式|为子系统中的一组接口提供一个一致的界面， 外观模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。|
|享元模式|通过共享以便有效的支持大量小颗粒对象。|
|代理模式|为其他对象提供一个代理以控制对这个对象的访问。|
|**行为型**||
|责任链模式|为解除请求的发送者和接收者之间耦合，而使多个对象都有机会处理这个请求。将这些对象连成一条链，并沿着这条链传递该请求，直到有一个对象处理它。|
|命令模式|将一个请求封装为一个对象，从而使你可用不同的请求对客户进行参数化；对请求排队或记录请求日志，以及支持可取消的操作。|
|迭代器模式|提供一种方法顺序访问一个聚合对象中各个元素, 而又不需暴露该对象的内部表示。|
|中介者模式|包装了一系列对象相互作用的方式，使得这些对象不必相互明显作用，从而使它们可以松散偶合。当某些对象之间的作用发生改变时，不会立即影响其他的一些对象之间的作用，保证这些作用可以彼此独立的变化。	|
|备忘录模式|备忘录对象是一个用来存储另外一个对象内部状态的快照的对象。备忘录模式的用意是在不破坏封装的条件下，将一个对象的状态捉住，并外部化，存储起来，从而可以在将来合适的时候把这个对象还原到存储起来的状态。|
|观察者模式|在对象间定义一个一对多的联系性，由此当一个对象改变了状态，所有其他相关的对象会被通知并且自动刷新。|
|访问者模式（难）|封装一些施加于某种数据结构元素之上的操作。一旦这些操作需要修改，接受这个操作的数据结构可以保持不变。访问者模式适用于数据结构相对未定的系统，它把数据结构和作用于结构上的操作之间的耦合解脱开，使得操作集合可以相对自由的演化。|
|策略模式|定义一个算法的系列，将其各个分装，并且使他们有交互性。策略模式使得算法在用户使用的时候能独立的改变。|
|状态模式|让一个对象在其内部状态改变的时候，其行为也随之改变。状态模式需要对每一个系统可能取得的状态创立一个状态类的子类。当系统的状态变化时，系统便改变所选的子类。|
|模板方法模式|模板方法模式准备一个抽象类，将部分逻辑以具体方法及具体构造子类的形式实现，然后声明一些抽象方法来迫使子类实现剩余的逻辑。不同的子类可以以不同的方式实现这些抽象方法，从而对剩余的逻辑有不同的实现。先构建一个顶级逻辑框架，而将逻辑的细节留给具体的子类去实现。|

--- 
# 参考资料
[1] [设计模式 (计算机) - 维基百科
](https://zh.wikipedia.org/wiki/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F_(%E8%AE%A1%E7%AE%97%E6%9C%BA))
[2] [超简化描述(简体中文版)](https://github.com/haiiiiiyun/design-patterns-for-humans-cn)

--- 
# 相关设计模式资料推荐
[1] [DesignPatternsPHP](https://github.com/domnikl/DesignPatternsPHP)
[2] [DesignPatternsPHP 中文版](https://learnku.com/docs/php-design-patterns/2018/)
[3] [图说设计模式](https://github.com/me115/design_patterns)

---
# 翻译说明
1. 当前翻译版本为 `5050fe7` on Nov 29, 2018
2. 本翻译内容未完全按照原文翻译，个人认为有些例子不够恰当，从而举了其他例子来说明。也有一些直译后难以理解的句子也进行了相应的本地化处理

---
# TODO
- [ ] 翻译校审
- [ ] 排版优化

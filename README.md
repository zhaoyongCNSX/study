[TOC]
 
 
# 相关文档
- Qt5属性系统在线文档: <http://doc.qt.io/qt-5/properties.html>
- 可查看 Qt 帮助文档 **The Property System** 部分.
- 官方文档的翻译: <http://www.cnblogs.com/linannk/p/5551660.html>
 
 
# 概述
Qt 元对象系统内容之一就是 Qt 动态属性系统 (Qt Property System) .
- 可用于在对象中动态添加属性, 读取属性值...
 
 
# `Q_PROPERTY()` 宏
```
Q_PROPERTY(type name    // [1]
           ( // 2.1 和 2.2 二选一.
             READ getFunction [WRITE setFunction] |     // [2.1]
             MEMBER memberName [(READ getFunction | WRITE setFunction)] // [2.2]
           )
           [RESET resetFunction]    // [3]
           [NOTIFY notifySignal]    // [4]
           [REVISION int]           // [5]
           [DESIGNABLE bool]        // [6]
           [SCRIPTABLE bool]        // [7]
           [STORED bool]            // [8]
           [USER bool]              // [9]
           [CONSTANT]               // [10]
           [FINAL])                 // [11]
```
- [1] type: 本属性类型  name: 本属性在元对象系统中的名字(可与具体变量名不同)
- [2] 如果没有 READ 函数, 就要有 MEMBER.
    - READ 函数要求: 一个常成员函数，其返回类型须是 属性类型 或是属性类型的常引用
    - WRITE 函数要求: 用于设置数据成员的值. 返回 void, 仅能有一个参数, 参数类型为属性类型或者是 属性类型的指针/引用.
- [2.1] 必须有读取函数, 设置函数可选.
- [2.2] 使用MEMBER 指定成员变量, 使得在没有 READ, WRITE 的情况下, 可访问成员变量.
- [3] RESET 函数可选, 用于将属性设置为上下文指定的默认值.
    - RESET 函数必须返回 void 类型，而且不带参数。
- [4] NOTIFY 信号可选, 用于指定一个存在的信号, 用户需保证在属性值被真正改变时发射该信号.
    - 该信号的要求: 0-1个参数, 参数类型必须与属性类型同. 参数为数据成员的新值.
- [5] REVISION 可选. 定义该函数被特定版本的API使用(通常为QML). 未包含该关键字时, 默认为 0.
- [6] DESIGNABLE 可选. 指定了该属性在 GUI 编辑器中是否可见(Qt Designer). 默认为 true. 可设置的值: true, false, boolean 成员函数.
- [7] SCRIPTABLE 可选. 指定该属性是否可被 script 引擎访问, 默认为 true. 可设置的值: true, false, boolean 成员函数.
- [8] STORED 可选. 指定该属性是独立存在, 还是依赖其他属性. 也指定了当保存对象属性时是否会保存该属性。大多数的属性的STORED为真。但是，QWidget::minmunWidth()的STROED为false，因为它的值是从QQWidget::minimumSize()中取得的，它的类型是QSize。
- [9] USER 可选. 指定了属性是否用户可见和可编辑的。正常情况下，每一个对象只用一个USER属性（默认为false）。例如，QAbstractButton::clicked对Buttons是可编辑的（checkable)。注，QItemDelegate 使用设置和访问函数设置 widget 的 USER 属性。
- [10] CONSTANT 出现表明属性为一个常量值. 指定了该关键字, 则不能有 WRITE 函数和 NOTIFY 信号. 对于同一个对象, 每一次READ函数的调用都应该返回相同的值.
- [11] FINAL 可选, 指定该属性不会在子类中被覆盖.
- READ WRITE RESET 函数可以被继承。它们也可以是虚函数。当在使用多继承的类中使用的时候，其必须来自第一个类。
- 属性类型可以是任何 QVariant 支持的属性，或者是用户自定义的属性。在这个例子中，类 QDate 被看做用户自定义的类型。
    ```cpp
    // 因为QDate是用户自定义的，当声明属性时，你必须包含<QDate>头文件。
    Q_PROPERTY(QDate data READ getDate WRITE setDate)
    ```
- 由于历史原因，QMap和QList是QVariantMap和QVariantList的同义词。
 
 
- [未完成] "使用元对象系统读写属性" 之后的内容
 
示例:
```cpp
 
class MyClass: public QObject
{
    Q_OBJECT
    Q_PROPERTY( int age READ age WRITE SetAge )
    // MEMBER
    Q_PROPERTY( int num MEMBER m_num )
 
public:
    int age(){ return m_age; }
    void SetAge(int &age){ m_age = age; }
private:
    int m_age;
    int m_num;
};
```


![waittingAnimation](./assets/waittingAnimation.gif)


![输入图片说明](https://images.gitee.com/uploads/images/2018/0829/123312_e2763df4_124771.png "default.png")



# Employee练习

**定 义 一 个 Employee 类**
该 类 包 含 ： private 成 员 变 量 name,age,birthday ， 其 中 birthday 为 
MyDate 类 的 对 象 ； 
并 为 每 一 个 属 性 定 义 getter,setter 方 法 ； 
并 重 写 tostring 方 法 输 出 name, age, birthday 
**MyDate 类 包 含 ：**
private 成 员 变 量 year,month,day; 并 为 每 一 个 属 性 定 义 getter, setter 
**方 法 ：** 
创 建 该 类 的 5 个 对 象 ， 并 把 这 些 对 象 放 入 Treeset 集 合 中 （ 下 一 章 ： 
Treeset 需 使 用 泛 型 来 定 义 ） 
分 别 按 以 下 两 种 方 式 对 集 合 中 的 元 素 进 行 排 序 ， 并 遍 历 输 出 ： 
1)  使 Employee 实 现 Comparable 接 口 ， 并 按 name 排 序 
2)  创 建 Treeset 时 传 入 comparator 对 象 ， 按 生 日 日 期 的 先 后 排 序 

[Toc]

## 未使用泛型
**源码如下：**

```java
package test;

import java.util.Comparator;
import java.util.Iterator;
import java.util.TreeSet;

public class TreeSetTest {
    public static void main(String[] args) {
        Employee employee1 = new Employee("a01",18,new MyDate(2012,7,6));
        Employee employee2 = new Employee("c02",15,new MyDate(2402,5,16));
        Employee employee3 = new Employee("z03",14,new MyDate(3002,11,3));
        Employee employee4 = new Employee("f04",28,new MyDate(4002,5,4));
        Employee employee5 = new Employee("q05",35,new MyDate(2032,6,24));

        TreeSet set = new TreeSet(new Comparator() {
            @Override
            public int compare(Object o1, Object o2) {
                if (o1 instanceof Employee && o2 instanceof Employee) {
                    Employee employee1 = (Employee) o1;
                    Employee employee2 = (Employee) o2;

                    MyDate employee1Birthday = employee1.getBirthday();
                    MyDate employee2Birthday = employee2.getBirthday();

                    int subYear = employee1Birthday.getYear() - employee2Birthday.getYear();
                    if (subYear != 0) {
                        return subYear;
                    }

                    int subMonth = employee1Birthday.getMonth() - employee2Birthday.getMonth();
                    if (subMonth != 0) {
                        return subMonth;
                    }

                    return employee1Birthday.getDay() - employee2Birthday.getDay();

                }
                throw new RuntimeException("传入的类型不一致");
            }
        });

        set.add(employee1);
        set.add(employee2);
        set.add(employee3);
        set.add(employee4);
        set.add(employee5);
        Iterator iterator = set.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }


    }

}

class Employee {
    private String name;
    private int age;
    private MyDate birthday;

    public Employee(String name, int age, MyDate birthday) {
        this.name = name;
        this.age = age;
        this.birthday = birthday;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public MyDate getBirthday() {
        return birthday;
    }

    public void setBirthday(MyDate birthday) {
        this.birthday = birthday;
    }

    @Override
    public String toString() {
        return "Employee{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", birthday=" + birthday +
                '}';
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        Employee employee = (Employee) o;

        if (age != employee.age) return false;
        if (name != null ? !name.equals(employee.name) : employee.name != null) return false;
        return birthday != null ? birthday.equals(employee.birthday) : employee.birthday == null;
    }

    @Override
    public int hashCode() {
        int result = name != null ? name.hashCode() : 0;
        result = 31 * result + age;
        result = 31 * result + (birthday != null ? birthday.hashCode() : 0);
        return result;
    }

//    @Override
//    public int compareTo(Object o) {
//        if (o instanceof Employee) {
//            Employee employee = (Employee) o;
////            if (this.age > employee.age) {
////                return 1;
////            } else if (this.age < employee.age) {
////                return -1;
////            } else {
////                return 0;
////            }
////        }
//            return this.name.compareTo(employee.name);
//        }
//        throw new RuntimeException("传入类型不一致");
//    }

}

class MyDate {
    private int year;
    private int month;
    private int day;

    public MyDate(int year, int month, int day) {
        this.year = year;
        this.month = month;
        this.day = day;
    }

    public int getYear() {
        return year;
    }

    public void setYear(int year) {
        this.year = year;
    }

    public int getMonth() {
        return month;
    }

    public void setMonth(int month) {
        this.month = month;
    }

    public int getDay() {
        return day;
    }

    public void setDay(int day) {
        this.day = day;
    }

    @Override
    public String toString() {
        return "MyDate{" +
                "year=" + year +
                ", month=" + month +
                ", day=" + day +
                '}';
    }
}
```
## 应用泛型后
```java
package test;

import java.util.Comparator;
import java.util.Iterator;
import java.util.TreeSet;

public class TreeSetTest {
    public static void main(String[] args) {
        Employee employee1 = new Employee("a01",18,new MyDate(2012,7,6));
        Employee employee2 = new Employee("c02",15,new MyDate(2402,5,16));
        Employee employee3 = new Employee("z03",14,new MyDate(3002,11,3));
        Employee employee4 = new Employee("f04",28,new MyDate(4002,5,4));
        Employee employee5 = new Employee("q05",35,new MyDate(2032,6,24));

        TreeSet<Employee> set = new TreeSet<>(new Comparator<Employee>() {
            @Override
            public int compare(Employee o1, Employee o2) {
                    MyDate employee1Birthday = o1.getBirthday();
                    MyDate employee2Birthday = o2.getBirthday();

                    int subYear = employee1Birthday.getYear() - employee2Birthday.getYear();
                    if (subYear != 0) {
                        return subYear;
                    }

                    int subMonth = employee1Birthday.getMonth() - employee2Birthday.getMonth();
                    if (subMonth != 0) {
                        return subMonth;
                    }

                    return employee1Birthday.getDay() - employee2Birthday.getDay();

            }
//            @Override
//            public int compare(Object o1, Object o2) {
//                if (o1 instanceof Employee && o2 instanceof Employee) {
//                    Employee employee1 = (Employee) o1;
//                    Employee employee2 = (Employee) o2;
//
//                    MyDate employee1Birthday = employee1.getBirthday();
//                    MyDate employee2Birthday = employee2.getBirthday();
//
//                    int subYear = employee1Birthday.getYear() - employee2Birthday.getYear();
//                    if (subYear != 0) {
//                        return subYear;
//                    }
//
//                    int subMonth = employee1Birthday.getMonth() - employee2Birthday.getMonth();
//                    if (subMonth != 0) {
//                        return subMonth;
//                    }
//
//                    return employee1Birthday.getDay() - employee2Birthday.getDay();
//
//                }
//                throw new RuntimeException("传入的类型不一致");
//            }
        });

        set.add(employee1);
        set.add(employee2);
        set.add(employee3);
        set.add(employee4);
        set.add(employee5);
        Iterator<Employee> iterator = set.iterator();
        while (iterator.hasNext()) {
            Employee employee = iterator.next();
            System.out.println(employee);
        }


    }

}

class Employee {
    private String name;
    private int age;
    private MyDate birthday;

    public Employee(String name, int age, MyDate birthday) {
        this.name = name;
        this.age = age;
        this.birthday = birthday;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public MyDate getBirthday() {
        return birthday;
    }

    public void setBirthday(MyDate birthday) {
        this.birthday = birthday;
    }

    @Override
    public String toString() {
        return "Employee{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", birthday=" + birthday +
                '}';
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        Employee employee = (Employee) o;

        if (age != employee.age) return false;
        if (name != null ? !name.equals(employee.name) : employee.name != null) return false;
        return birthday != null ? birthday.equals(employee.birthday) : employee.birthday == null;
    }

    @Override
    public int hashCode() {
        int result = name != null ? name.hashCode() : 0;
        result = 31 * result + age;
        result = 31 * result + (birthday != null ? birthday.hashCode() : 0);
        return result;
    }

//    @Override
//    public int compareTo(Object o) {
//        if (o instanceof Employee) {
//            Employee employee = (Employee) o;
////            if (this.age > employee.age) {
////                return 1;
////            } else if (this.age < employee.age) {
////                return -1;
////            } else {
////                return 0;
////            }
////        }
//            return this.name.compareTo(employee.name);
//        }
//        throw new RuntimeException("传入类型不一致");
//    }

}

class MyDate {
    private int year;
    private int month;
    private int day;

    public MyDate(int year, int month, int day) {
        this.year = year;
        this.month = month;
        this.day = day;
    }

    public int getYear() {
        return year;
    }

    public void setYear(int year) {
        this.year = year;
    }

    public int getMonth() {
        return month;
    }

    public void setMonth(int month) {
        this.month = month;
    }

    public int getDay() {
        return day;
    }

    public void setDay(int day) {
        this.day = day;
    }

    @Override
    public String toString() {
        return "MyDate{" +
                "year=" + year +
                ", month=" + month +
                ", day=" + day +
                '}';
    }
}
```
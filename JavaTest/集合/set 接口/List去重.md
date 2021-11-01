# List去重
练习：在List内去除重复数字值，要求尽量简单
```java
package test;

import java.util.ArrayList;
import java.util.HashSet;
import java.util.Iterator;

public class CollectionTest {
    public static void main(String[] args) {
        ArrayList list = new ArrayList();
        list.add(1);
        list.add(2);
        list.add(2);
        list.add(4);
        list.add(4);

        list = duplicate(list);

        Iterator iterator = list.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
    }
    public static ArrayList duplicate (ArrayList list) {
        HashSet set = new HashSet();
        set.addAll(list);
        return new ArrayList(set);
    }
}
```
# RemoveTest
```java
@Test 
public void testListRemove() { 
	List list = new ArrayList(); 
	list. add(1); 
	list. add(2); 
	list. add(3); 
	updateList(1ist); 
	System. out. println(list) ; // 
}

private static void updateList(List list) { 
	list. remove(2); 
}
```
**答案如下:**
```
public class ListExer {
    /*
    区分List中remove(int index)和remove(Object obj)
     */
    @Test
    public void testListRemove() {
        List list = new ArrayList();
        list.add(1);
        list.add(2);
        list.add(3);
        updateList(list);
        System.out.println(list);// 1,2
    }

    private void updateList(List list) {
//        list.remove(2);
        list.remove(new Integer(2));
    }

}
```


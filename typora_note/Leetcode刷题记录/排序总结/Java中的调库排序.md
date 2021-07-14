

## 对int[]类型的排序

```
//        默认升序
        int scores[] = new int[]{1,2,3,89,4};
        Arrays.sort(scores);
```

**如果想要降序怎么办呢？**需要注意的是 **不能使用基本类型（int,double, char）**，如果是int型需要改成Integer，float要改成Float

```
  Integer scores[] = {1,2,3,89,4};
        Arrays.sort(scores,Collections.reverseOrder());
        for (Integer i:scores) {
            System.out.println(i);
        }
```

**如果只给了int[]怎么办呢？需要先转换一下**

```
//        如果得到的是int数组，怎么办，需要先转换一下
         int scores[] = new int[]{1,2,3,89,4};
        Integer newScores[] = new Integer [5];
        for(int i=0;i<scores.length;i++){
            newScores[i]= new Integer(scores[i]);
        }

        Arrays.sort(newScores,Collections.reverseOrder());
        for (Integer i:newScores
        ) {
            System.out.println(i);
        }
```

然后刷题的时候找到的方法：

方法1：**利用list**

```java
int nums[]=new int[]{2,5,3,8,6,7,0,13,65,3,7};
        List<Integer> nums2=Arrays.stream(nums).boxed().collect(Collectors.toList());
        Collections.sort(nums2, Collections.reverseOrder());
        System.out.println(nums2);

```

方法2：直接新建个数组，然后从头到尾赋值就完事了

```java
 Arrays.sort(nums);
        int nums2[]=new int[nums.length];
        for(int i=0;i<nums.length;i++)
        {
            nums2[i]=nums[nums.length-1-i];//从大到小排序
            // System.out.println("nums2:"+ nums2[i]);
        }
```



还可以这么来：

```java
 ArrayList<Integer> arr1 = new ArrayList<Integer>();
 arr1.sort(new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o1-o2;
            }
        });

 arr1.sort(Comparator.reverseOrder());
 arr1.sort(Comparator.naturalOrder());
```



## 对char数组的排序

```java
String s="abcdef";
char[] arrayCh = s.toCharArray(); //1，把sortStr转换为字符数组
Arrays.sort(arrayCh);
s=Arrays.toString(arrayCh);//排序
System.out.println(s);
```

输出：

```
[a, b, c, d, e, f]
```



## 比较两个字符串的大小

```java
 String sba="5467";
        String sbb="5919";
        int a=sba.substring(0,sba.length()).compareTo(sbb.substring(0,sbb.length()));
        System.out.println(a);
```

这边直接比较字符串从头到尾的大小的，一位一位比过去。

输出

```
-5
```



## **二维数组的排序：**

```java
//最开始的代码：
 //        二维数组排序
        int [][] points2=new int[][]{{3,9},{7,12},{3,8},{6,8},{9,10},{6,8},{2,9},{0,9},{3,9},{0,6},{2,8}};
        Arrays.sort(points2, new Comparator<int[]>() {
            @Override
            public int compare(int[] o1, int[] o2) {
                return o1[1] - o2[1];//按照第二个数据进行排序 但是这样容易越界
            }
        });
        printArr(points2);

```

结果

```
[0, 6]
[3, 8]
[6, 8]
[6, 8]
[2, 8]
[3, 9]
[2, 9]
[0, 9]
[3, 9]
[9, 10]
[7, 12]
```



```java
  Arrays.sort(points, new Comparator<int[]>() {
            @Override
            public int compare(int[] o1, int[] o2) {
                if(o1[1]<o2[1])//这样就不容易出现越界的情况
                {
                    return -1;
                }
                else
                {
                    return 1;
                }
            }
        });
```



## 优先级队列

```
PriorityQueue<Integer> minHeap = new PriorityQueue<Integer>(); //小顶堆，默认容量为11
PriorityQueue<Integer> maxHeap = new PriorityQueue<Integer>(11,new Comparator<Integer>(){ //大顶堆，容量11
    @Override
    public int compare(Integer i1,Integer i2){
        return i2-i1;
    }
});
```

优先级队列会有溢出的问题：

详情：https://www.cnblogs.com/angelica-duhurica/p/12592230.html
这里复述一下：

最大堆原始

```java
 		Queue<Integer> heap = new PriorityQueue<>(new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o2 - o1;
            }
        });
```

用lambda简化后：

```java
Queue<Integer> heap = new PriorityQueue<>((o1, o2) -> o2 - o1);
```

注意到，**这里o2-o1是会产生溢出的，会导致结果不正确。**

所以采用这种写法更好：

```java
 		Queue<Integer> heap = new PriorityQueue<>(new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o2.compareTo(o1);
            }
        });
```



```java
Queue<Integer> heap = new PriorityQueue<>((o1, o2) -> o2.compareTo(o1));
```



## 二维数组优先级队列

```java
int[][] heights
 // 优先队列中存放三元组 [x,y,h] 坐标和高度
PriorityQueue<int[]> pq = new PriorityQueue<>((o1, o2) -> o1[2] - o2[2]);//这样会越界
pq.offer(new int[]{i, j, heights[i][j]});

作者：jerry_nju
链接：https://leetcode-cn.com/problems/trapping-rain-water-ii/solution/you-xian-dui-lie-de-si-lu-jie-jue-jie-yu-shui-ii-b/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



修改

```java
PriorityQueue<int[]> pq = new PriorityQueue<>(new Comparator<int[]>(){ //小顶堆，容量11
            @Override
            public int compare(int[] o1,int[] o2){
                if(o1[2]<o2[2])//这样就不容易出现越界的情况
                {
                    return -1;
                }
                else
                {
                    return 1;
                }
            }
        });

pq.offer(new int[]{i, j, heights[i][j]});//每一个元素存的分别是坐标点 和 对应的深浅
```



修改

```java
class Solution {
    public int eatenApples(int[] apples, int[] days) {
        PriorityQueue<int[]> q = new PriorityQueue<>(Comparator.comparingInt(x -> x[0]));
    }
}

或者
   	PriorityQueue<int[]> queue = new PriorityQueue<>((o1, o2) -> o1[1] < o2[1] ? -1 : 1);

作者：zhayujie
链接：https://leetcode-cn.com/problems/maximum-number-of-eaten-apples/solution/you-xian-dui-lie-you-xian-chi-zui-zao-gu-3ti9/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

```





# java 对HashMap 进行排序，优先值value排序，若value相同时对键KEY按字母表顺序排序

https://blog.csdn.net/u014388729/article/details/80156645



Java中HashMap是一种用于存储“键”和“值”信息对的数据结构。不同于Array、ArrayList和LinkedLists，它不会维持插入元素的顺序。因此对HashMap排序的掌握很重要。HashMap排序有两个步骤：

一.通过map.entrySet()方法获取键值对;并将键值对传递给链表list；

```
List<Map.Entry<String,Integer>> list = new ArrayList<>();
        list.addAll(map.entrySet());
```

二.自定义比较器来使用Collections.sort()方法排序链表。

```
Collections.sort(list,new Comparator<Map.Entry<String,Integer>>(){
            public int compare(Map.Entry<String,Integer>e1,Map.Entry<String,Integer>e2){
                int re = e2.getValue().compareTo(e1.getValue());
                if(re!=0){return re;}
                else{return e1.getKey().compareTo(e2.getKey());}
            }
        });
```

例题：

下面来看看具体实例来演示如何排序Value：

题一：给定一个非空的整数数组，返回其中出现频率前 ***k\*** 高的元素。

例如，

给定数组 `[1,1,1,2,2,3]` , 和 k = 2，返回 `[1,2]`。

**注意：**

- 你可以假设给定的 *k* 总是合理的，1 ≤ k ≤ 数组中不相同的元素的个数。
- 你的算法的时间复杂度**必须**优于 O(*n* log *n*) , *n* 是数组的大小。

运用以上方法解答：

```java
class Solution {
    public List<Integer> topKFrequent(int[] nums, int k) {
        List<Integer> list = new ArrayList<Integer>();
        Map<Integer,Integer>intMap = new HashMap<Integer,Integer>();
        for(int i:nums){
            if(intMap.get(i) == null){
                intMap.put(i,1);
            }
            else{
                int t = intMap.get(i)+1;
                intMap.put(i,t);
            }
        }
        List<Map.Entry<Integer,Integer>>li = new LinkedList<Map.Entry<Integer,Integer>>();
        li.addAll(intMap.entrySet());
        Collections.sort(li,new Comparator<Map.Entry<Integer,Integer>>(){
            public int compare(Map.Entry<Integer,Integer>e1,Map.Entry<Integer,Integer>e2){
                return e2.getValue().compareTo(e1.getValue()); 
            }
        });
        int tem = 0;
        for(Iterator <Map.Entry<Integer,Integer>>it = li.iterator(); it.hasNext();){
            list.add(it.next().getKey());
            tem++;
            if(tem ==k){break;}
            
        }
        return list;
    }
}
```

以上题目再复杂一点有：（先按value排序 再按key 字典序排序）

给一非空的单词列表，返回前 *k* 个出现次数最多的单词。

返回的答案应该按单词出现频率由高到低排序。如果不同的单词有相同出现频率，按字母顺序排序。

```
示例 1：
输入: ["i", "love", "leetcode", "i", "love", "coding"], k = 2
输出: ["i", "love"]
解析: "i" 和 "love" 为出现次数最多的两个单词，均为2次。
    注意，按字母顺序 "i" 在 "love" 之前。
```

```
示例 2：
输入: ["the", "day", "is", "sunny", "the", "the", "the", "sunny", "is", "is"], k = 4
输出: ["the", "is", "sunny", "day"]
解析: "the", "is", "sunny" 和 "day" 是出现次数最多的四个单词，
    出现次数依次为 4, 3, 2 和 1 次。
```

**注意：**

1. 假定 *k* 总为有效值， 1 ≤ *k* ≤ 集合元素数。

2. 输入的单词均由小写字母组成

   ```java
   import java.util.Map.Entry;
   class Solution {
       public List<String> topKFrequent(String[] words, int k) {
           List<String> result = new ArrayList<>();
           Map<String,Integer> map = new HashMap<String,Integer>();
           
           for(String a:words){
               String b = a;
               if(map.get(b)==null){map.put(b,1);}
               else{
                   int c = map.get(b) + 1;
                   map.put(b,c);
               }
           }
           //Set<Entry<String,Integer>> entry = map.entrySet();
           List<Entry<String,Integer>> list = new ArrayList<>();
           list.addAll(map.entrySet());
           Collections.sort(list,new Comparator<Map.Entry<String,Integer>>(){
               public int compare(Map.Entry<String,Integer>e1,Map.Entry<String,Integer>e2){
                   int re = e2.getValue().compareTo(e1.getValue());
                   if(re!=0){return re;}
                   else{return e1.getKey().compareTo(e2.getKey());}
               }
           });
           for(int i=0;i<k;i++){
               result.add(list.get(i).getKey());
           }
           return result;
       }
   }
   ```

   个人总结：灵活使用Comparator接口可以实现HashMap的灵活排序：包括分别对键、值的升降序排序，优先按值、其次按键排序，或者优先按键、其次按值排序；以上实现只需重写compare方法。

## 总结重写Comparetor中的compare方法

1.对值value降序排序：

```java
 public int compare(Map.Entry<Integer,Integer>e1,Map.Entry<Integer,Integer>e2){
                return e2.getValue().compareTo(e1.getValue()); 
            }
```

2.对值value升序排序：

```java
 public int compare(Map.Entry<Integer,Integer>e1,Map.Entry<Integer,Integer>e2){
                return e1.getValue().compareTo(e2.getValue()); 
            }
```

3.对键KEY降序排列：

```java
 public int compare(Map.Entry<Integer,Integer>e1,Map.Entry<Integer,Integer>e2){
                return e2.getKey().compareTo(e1.getKey()); 
            }
```

4.对键KEY升序排列：

```java
 public int compare(Map.Entry<Integer,Integer>e1,Map.Entry<Integer,Integer>e2){
                return e1.getKey().compareTo(e2.getKey()); 
            }
```

5.优先按值value降序、其次按键key升序排序：

```java
public int compare(Map.Entry<String,Integer>e1,Map.Entry<String,Integer>e2){
                int re = e2.getValue().compareTo(e1.getValue());
                if(re!=0){return re;}
                else{return e1.getKey().compareTo(e2.getKey());}
            }
```

6.优先按键key降序、其次按值value升序排序：

```java
public int compare(Map.Entry<String,Integer>e1,Map.Entry<String,Integer>e2){
                int re = e2.getKey().compareTo(e1.getKey());
                if(re!=0){return re;}
                else{return e1.getValue().compareTo(e2.getValue());}
            }
```
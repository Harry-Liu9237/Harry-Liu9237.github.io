### 关于判断一个字符串是否对称?
public class StringBufferExercise{  <br/>
  public static void main(String[] args){ <br/>
    Scaaner sc = new Scanner(System.in); <br/>
    String s = sc.nextLine(); <br/>
    boolean b2 = isSame(s); <br/>
  } <br/>
  public static boolean isSame(String s){ <br/>
    return new StringBuffer(s).reverse().toString().equals(s); <br/>
  } <br/>
}
### StringBuffer和数组的区别?
    两者都可以看做是一个容器，装其他的数据；
    但是呢，StringBuffer的数据最终是一个字符串数据；而数组可以放置多种数据，但必须是同一种数据类型的

### 形式参数问题
     String作为参数传递和StringBuffer作为参数传递
     
    形式参数：
        基本类型：形式参数的改变不影响实际参数
        引用类型：形式参数的改变直接影响实际参数
        注意：String作为参数传递，效果和基本数据类型作为参数传递是一样的
### 数组排序之冒泡排序
    public class ArrayDemo{
      public static void main(String[] args){
          int[] arr = {13,2,45,88,67};
          printArray(arr);
          bubbleSort(arr);
      }
      //冒泡排序代码
      public static void bubbleSort(int[] arr){
          for(int x=0;x<arr.length-1;x++){
              for(int y=0;y<arr.length-1-x;y++){
                  if(arr[y]>arr[y+1]){
                      int temp = arr[y];
                      arr[y] = arr[y+1];
                      arr[y+1] = temp;
                  }
              }
          }
      }
      //遍历功能
      public static void printArray(int[] arr){
          System.out.print("[");
          for(int x=0;x<arr.length;x++){
            if(x==arr.length-1){
              System.out.print(arr[x]);
            }else{
              System.out.print(arr[x] + ", ");
            }
          }
          System.out.print("]");
      }
     
    }

### 把字符串中的字符进行排序

    public class ArrayTest{
      public static void main(String[] args){
      //定义一个字符串
      String s="dacgebf";
      
      //把字符串转换为字符数组
      char[] chs=s.toCharArray[];
      
      //把字符数组进行排序
      bubbleSort(chs);
      
      //把排序后的字符数组转成字符串
      String result = String.valueOf(chs);
      }
  
    //冒泡排序
     public static void bubbleSort(char[] chs){
      for(int x=0;x<chs.length-1;x++){
          for(int y=0;y<chs.length-1-x;y++){
            if(chs[y]>chs[y+1]){
              char temp = chs[y];
              chs[y] = chs[y+1];
              chs[y+1] = temp;
            }
          }
      }
    }
  }

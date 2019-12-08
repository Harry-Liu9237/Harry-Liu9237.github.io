<h1>1.StringBuffer与String之间的相互转化</h1>
public class StringBufferDemo{   <br/>
    public statis void main(String[] args){  <br/>
      // String ---StringBuffer
      String s = "hello";  <br/>
      //方式一：通过构造方法  <br/>
      StringBuffer sb = new StringBuffer(s);  <br/>
      //方式二:通过append方法  <br/>
      StringBuffer sb2 = new StringBuffer();  <br/>
      sb2.append(s);  <br/>
      // StringBuffer  --- String  <br/>
      StringBuffer buffer = new StringBuffer("java"); <br/>
      //通过构造方法  <br/>
      String str = new String(buffer);  <br/>
      //方式2：通过toString()方法   <br/>
      String str2 = buffer.toString(); 
    }
}
<h1>2.数组拼接字符串(StringBuffer方式)</h1>
public class StringBufferTest{  <br/>
     public statis void main(String[] args){  <br/>
        int[] arr = {11,22,33,44,55}; <br/>
        String str = arrayToString(arr); <br/>
     }<br/>
     
     public static String arrayToString(int[] arr){ <br/>
        StringBuffer sb = new StringBuffer(s); 
        sb.append("[");  
        for(int x=0; x<arr.length;x++){  
          if(x==arr.length-1){  
              sb.append(arr[x]);  
          }else { 
            sb.append(arr[x]).append(",");  
          }  
        }  
     }  
}  
<h1>3.字符串反转</h1>
public class StringBufferTest2{  <br/>
     public statis void main(String[] args){  <br/>
        Scanner sc = new Scanner(System.in); <br/>
        System.out.println("请输入数据");<br/>
        String s = sc.nextLine();
        
        //用String做拼接
        myReverse(s);
        //用StringBuffer的reverse()做拼接
        myReverse2(s);
     }<br/>
     
     //用String做拼接
     public static String myReverse(String s){
          String result = "";
          char[] chs = s.toCharArray();
          for(int x = chs.length -1; x>=0;x--){
            result += chs[x];
          }
          return result;
     }
     //用StringBuffer的reverse()做拼接
      public static String myReverse2(String s){
        //StringBuffer sb = new StringBuffer();
        //sb.append(s);
        
        //StringBuffer sb = new StringBuffer(s);
        // sb.reverse();
        //return sb.toString();
        
        //简易版
        return new StringBuffer(s).reverse().toString();
     }
}  


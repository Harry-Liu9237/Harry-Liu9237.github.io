<h1>一 先说下队列</h1>
<p>队列是一种数据结构．它有两个基本操作：在队列尾部加入一个元素，和从队列头部移除一个元素（注意不要弄混队列的头部和尾部）</p>
<p>就是说，队列以一种先进先出的方式管理数据，如果你试图向一个 已经满了的阻塞队列中添加一个元素或者是从一个空的阻塞队列中移除一个元索，
将导致线程阻塞．</p>
<p>在多线程进行合作时，阻塞队列是很有用的工具。
工作者线程可以定期地把中间结果存到阻塞队列中而其他工作者线程把中间结果取出并在将来修改它们。
队列会自动平衡负载。</p>
<p>如果第一个线程集运行得比第二个慢，则第二个 线程集在等待结果时就会阻塞。如果第一个线程集运行得快，那么它将等待第二个线程集赶上来.</p>
<p>说白了,就是先进先出,线程安全!</p>
<p>java中并发队列都是在java.util.concurrent并发包下的,Queue接口与List、Set同一级别，都是继承了Collection接口,
最近学习了java中的并发Queue的所有子类应用场景,这里记录分享一下:</p>
<p>1.1 这里可以先用wait与notify(脑忒fai) 模拟一下队列的增删数据,简单了解一下队列:</p>

```
import java.util.LinkedList;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.atomic.AtomicInteger;
/**
 * 模拟队列增删数据
 * @author houzheng
 */
public class MyQueue {
    //元素集合
    private LinkedList<Object> list=new LinkedList<Object>();
    //计数器(同步),判断集合元素数量
    private AtomicInteger count=new AtomicInteger();
    //集合上限与下限,final必须指定初值
    private final int minSize=0;
    private final int maxSize;
    //构造器指定最大值
    public MyQueue(int maxSize) {
        this.maxSize = maxSize;
    }
     
    //初始化对象,用于加锁,也可直接用this
    private Object lock=new Object();
    //put方法:往集合中添加元素,如果集合元素已满,则此线程阻塞,直到有空间再继续
    public void put(Object obj){
        synchronized (lock) {
            while(count.get()==this.maxSize){
                try {
                    lock.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();}
            }
            list.add(obj);
            //计数器加一
            count.incrementAndGet();
            System.out.println("放入元素:"+obj);
            //唤醒另一个线程,(处理极端情况:集合一开始就是空,此时take线程会一直等待)
            lock.notify();
        }
    }
    //take方法:从元素中取数据,如果集合为空,则线程阻塞,直到集合不为空再继续
    public Object take(){
        Object result=null;
        synchronized(lock){
            while(count.get()==this.minSize){
                try {
                    lock.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();}
            }
            //移除第一个
            result=list.removeFirst();
            //计数器减一
            count.decrementAndGet();
            System.out.println("拿走元素:"+result);
            //唤醒另一个线程,(处理极端情况:集合一开始就是满的,此时put线程会一直等待)
            lock.notify();
        }
        return result;
    }
    public int getSize(){
        return this.count.get();
    }
    public static void main(String[] args) {
        //创建集合容器
        MyQueue queue=new MyQueue(5);
        queue.put("1");
        queue.put("2");
        queue.put("3");
        queue.put("4");
        queue.put("5");
        System.out.println("当前容器长度为:"+queue.getSize());
        Thread t1=new Thread(()->{
            queue.put("6");
            queue.put("7");
        },"t1");
        Thread t2=new Thread(()->{
            Object take1 = queue.take();
            Object take2 = queue.take();
        },"t2");
        //测试极端情况,两秒钟后再执行另一个线程
        t1.start();
        try {
            TimeUnit.SECONDS.sleep(2);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        t2.start();
    }
}
```
<p>这里用线程通信的方式简单模拟了队列的进出,那么接下来就正式进入java的并发队列:</p>
<h1>二 并发Queue</h1>
<p>JDK中并发队列提供了两种实现,一种是高性能队列ConcurrentLinkedQueue,一种是阻塞队列BlockingQueue,两种都继承自Queue:</p>
<h2>1 ConcurrentLinkedQueue</h2>
<p>这是一个使用于高并发场景的队列(额,各位看这块博客的小朋友,最好对线程基础比较熟悉再来看,当然我也在拼命学习啦,哈哈哈),主要是无锁的方式,他的想能要比BlockingQueue好,是基于链接节点的无界线程安全队列,先进先出,不允许有null元素,废话不多说,上demo:</p>

```
public void test(){
  ConcurrentLinkedQueue<String> clq = new ConcurrentLinkedQueue<String>();
  //add和offer都是添加元素，在ConcurrentLinkedQueue中没有任何区别
  clq.add("a");
  clq.add("b");
  clq.offer("c");
  clq.offer("d");
  System.out.println(clq.poll());//取出头部数据，会删除头部元素    a
  System.out.println(clq.peek());//取出头部数据，不会删除头部元素  b
  System.out.println(clq); [b,c,d]
}
```

<p>这种queue比较简单,没什么好说的,和ArrayList一样用就可以,关键是BlockingQUeue</p>
<h2>2 BlockingQueue</h2>
<p>blockingQueue主要有5中实现,我感觉都挺有意思的,其中几种还比较常用就都学习了下,这里都介绍下:</p>
<p>2.1  ArrayBlockingQueue</p>
<p>基于数组的阻塞队列的实现，在ArrayBlockingQueue内部维护了一个定长数组，以遍缓存队列中的数据对象，其内部没有实现读写分离，也就意味着
生产与并行不能完全分行，长度是需要定义的，可以指定先进先出，也可以指定先进后出，也叫有界队列，在很多场合非常适用</p>

```
@Test
public void test02() throws Exception{
    //必须指定队列长度
    ArrayBlockingQueue<String> abq=new ArrayBlockingQueue<String>(2);
    abq.add("a");
    //add :添加元素,如果BlockingQueue可以容纳,则返回true,否则抛异常,支持添加集合
    System.out.println(abq.offer("b"));//容量如果不够,返回false
    //offer: 如果可能的话,添加元素,即如果BlockingQueue可以容纳,则返回true,否则返回false,支持设置超时时间
    //设置超时,如果超过时间就不添加,返回false, 
    abq.offer("d", 2, TimeUnit.SECONDS);// 添加的元素,时长,单位
    //put 添加元素,如果BlockQueue没有空间,则调用此方法的线程被阻断直到BlockingQueue里面有空间再继续.
    abq.put("d");//会一直等待
    //poll 取走头部元素,若不能立即取出,则可以等time参数规定的时间,取不到时返回null,支持设置超时时间
    abq.poll();
    abq.poll(2,TimeUnit.SECONDS);//两秒取不到返回null
    //take()  取走头部元素,若BlockingQueue为空,阻断进入等待状态直到Blocking有新的对象被加入为止
    abq.take();
    //取出头部元素,但不删除
    abq.element();
    //drainTo()
    //一次性从BlockingQueue获取所有可用的数据对象（还可以指定获取数据的个数），通过该方法，可以提升获取数据效率；不需要多次分批加锁或释放锁。
    List list=new ArrayList();
    abq.drainTo(list,2);//将队列中两个元素取到list中，取走后队列中就没有取走的元素
    System.out.println(list); //[a,b]
    System.out.println(abq);  //[]
}
```
<h2>2.2 LinkedBlockingQueue</h2>

```
@Test
public void test03(){
    LinkedBlockingQueue lbq=new LinkedBlockingQueue();//可指定容量，也可不指定
    lbq.add("a");
    lbq.add("b");
    lbq.add("c");
    //API与ArrayBlockingQueue相同
    //是否包含
    System.out.println(lbq.contains("a"));
    //移除头部元素或者指定元素  remove("a")
    System.out.println(lbq.remove());
    //转数组
    Object[] array = lbq.toArray();
    //element 取出头部元素，但不删除
    System.out.println(lbq.element());
    System.out.println(lbq.element());
    System.out.println(lbq.element());
}
```

<h2>2.3 SynchronousQueue</h2>

```
public static void main(String[] args) {
    SynchronousQueue<String> sq=new SynchronousQueue<String>();
    // iterator() 永远返回空，因为里面没东西。
    // peek() 永远返回null
    /**
     * isEmpty()永远是true。
     * remainingCapacity() 永远是0。
     * remove()和removeAll() 永远是false。
     */
    new Thread(()->{
        try {
            //取出并且remove掉queue里的element（认为是在queue里的。。。），取不到东西他会一直等。
            System.out.println(sq.take());
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
         
    }).start();
    new Thread(()->{
        try {
            //offer() 往queue里放一个element后立即返回，
            //如果碰巧这个element被另一个thread取走了，offer方法返回true，认为offer成功；否则返回false
            //true ,上面take线程一直在等,
            ////下面刚offer进去就被拿走了,返回true,如果offer线程先执行,则返回false
            System.out.println(sq.offer("b"));
             
        } catch (Exception e) {
            e.printStackTrace();
        }
         
    }).start();
    new Thread(()->{
        try {
            //往queue放进去一个element以后就一直wait直到有其他thread进来把这个element取走
            sq.put("a");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }).start();
}
```

<h2>2.4 PriorityBlockingQueue</h2>

```
@Test
public void test04() throws Exception{
    //队列里元素必须实现Comparable接口,用来决定优先级
    PriorityBlockingQueue<String> pbq=new PriorityBlockingQueue<String>();
    pbq.add("b");
    pbq.add("g");
    pbq.add("a");
    pbq.add("c");
    //获取的时候会根据优先级取元素,插入的时候不会排序,节省性能
    //System.out.println(pbq.take());//a,获取时会排序,按优先级获取
    System.out.println(pbq.toString());//如果前面没有取值,直接syso也不会排序
    Iterator<String> iterator = pbq.iterator();
    while(iterator.hasNext()){
        System.out.println(iterator.next());
    }
}
@Test
public void test05(){
    PriorityBlockingQueue<Person> pbq=new PriorityBlockingQueue<Person>();
    Person p2=new Person("姚振",20);
    Person p1=new Person("侯征",24);
    Person p3=new Person("何毅",18);
    Person p4=new Person("李世彪",22);
    pbq.add(p1);
    pbq.add(p2);
    pbq.add(p3);
    pbq.add(p4);
    System.out.println(pbq);//没有按优先级排序
    try {
        //只要take获取元素就会按照优先级排序,获取一次就全部排好序了,后面就会按优先级迭代
        pbq.take();
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
    //按年龄排好了序
    for (Iterator iterator = pbq.iterator(); iterator.hasNext();) {
        Person person = (Person) iterator.next();
        System.out.println(person);
    }
}
```
<h2>最后说一下DelayQueue ,这里用个网上很经典的例子,网吧上网计时</h2>
<p>网民实体queue中元素</p>

```
//网民
public class Netizen implements Delayed {
    //身份证
private String ID;
//名字
private String name;
//上网截止时间
private long playTime;
//比较优先级,时间最短的优先
@Override
public int compareTo(Delayed o) {
    Netizen netizen=(Netizen) o;
    return this.getDelay(TimeUnit.SECONDS)-o.getDelay(TimeUnit.SECONDS)>0?1:0;
}
public Netizen(String iD, String name, long playTime) {
    ID = iD;
    this.name = name;
    this.playTime = playTime;
}
//获取上网时长,即延时时长
@Override
public long getDelay(TimeUnit unit) {
    //上网截止时间减去现在当前时间=时长
    return this.playTime-System.currentTimeMillis();
}
```
<p>网吧类:</p>

```
//网吧
public class InternetBar implements Runnable {
    //网民队列,使用延时队列
private DelayQueue<Netizen> dq=new DelayQueue<Netizen>();
//上网
public void startPlay(String id,String name,Integer money){
    //截止时间= 钱数*时间+当前时间(1块钱1秒)
    Netizen netizen=new Netizen(id,name,1000*money+System.currentTimeMillis());
    System.out.println(name+"开始上网计费......");
    dq.add(netizen);
}
//时间到下机
public void endTime(Netizen netizen){
    System.out.println(netizen.getName()+"余额用完,下机");
}
@Override
public void run() {
    //线程,监控每个网民上网时长
    while(true){
        try {
            //除非时间到.否则会一直等待,直到取出这个元素为止
            Netizen netizen=dq.take();
            endTime(netizen);
        }
        catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
 
}
public static void main(String[] args) {
    //新建一个网吧
    InternetBar internetBar=new InternetBar();
    //来了三个网民上网
    internetBar.startPlay("001","侯征",3);
    internetBar.startPlay("002","姚振",7);
    internetBar.startPlay("003","何毅",5);
        Thread t1=new Thread(internetBar);
        t1.start();
    }
}
```

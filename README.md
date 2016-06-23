###增加5.0后提出的Material Design中的FloatingActionButton，需要导入com.android.support:design:23.4.0包，因为是android5.0（API21）后才有的，所以须在新建一个layout-v21的包，以供android5.0（API21）及以上的手机调用，当然layout中也要有一个布局，以应对android5.0（API21）以下的手机进行适配
###增加SwipeRefreshLayout，SwipeRefreshLayout是谷歌官方提供的下拉刷新控件，可直接与ListView，RecyclerView,GridView等直接嵌套使用，不会冲突


### FloatingActionButton

FloatingActionButton 直接在xml中调用，和Button使用方法是一样的

### SwipeRefreshLayout
SwipeRefreshLayout也是直接在xml中调用，就和用LinearLayout等常用布局一样调用，不过SwipeRefreshLayout中只能嵌套一个layout，和ScrollView差不多

设置下拉圆圈上的颜色，蓝色、绿色、橙色、红色

```java
mainBinding.swipe.setColorSchemeResources(android.R.color.holo_blue_bright, android.R.color.holo_green_light,
                android.R.color.holo_orange_light, android.R.color.holo_red_light);

```

设置手指在屏幕下拉多少距离会触发下拉刷新

```java
        mainBinding.swipe.setDistanceToTriggerSync(400);

```


设置下拉动画的背景色
```java
        mainBinding.swipe.setProgressBackgroundColor(android.R.color.holo_red_light);
        
```      
  
设置下拉动画的大小
```java
        mainBinding.swipe.setSize(SwipeRefreshLayout.LARGE);
        
```  

设置下拉动画的下拉事件

```java
        mainBinding.swipe.setOnRefreshListener(new SwipeRefreshLayout.OnRefreshListener() {
            @Override
            public void onRefresh() {
                Log.i("main", "onRefresh called from SwipeRefreshLayout");
//                adapter.setFristData(new User("刷新", "刷新"), mainBinding.swipe);
                new OnRefreshAsyncTask().execute();//写一个异步，进行你的操作
                adapter.setFristData(new User("刷新", "刷新"));
//                mainBinding.recyclerView.smoothScrollBy(0, -1000);//通过沿任一轴的像素的规定量动画滚动。（水平，垂直）
                mainBinding.recyclerView.smoothScrollToPosition(0);//启动一个平滑滚动到适配器的位置。
            }
        });

```  

OnRefreshAsyncTask代码，包含停止刷新
```java
class OnRefreshAsyncTask extends AsyncTask<Void, Void, Void> {

        @Override
        protected Void doInBackground(Void... params) {
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return null;
        }


        @Override
        protected void onPostExecute(Void aVoid) {
            super.onPostExecute(aVoid);
            //停止刷新
            mainBinding.swipe.setRefreshing(false);
        }
    }
    
```

# DataBindingDemo
Data Binding Demo
谷歌官方提供的数据绑定框架，此gradle版本是2.1.2

###配置
因为2.1.2的gradle中已经集成了dataBinding，
所以只需要使用Data Binding的Moudle的build.gradle中添加以下属性

```java
android {
    ...
    dataBinding {
            enabled = true
        }
}

```

dataBinding大概在gradle1.5版本中就已经集成在一起了

###布局使用


起始根标签是 layout，接下来一个 data 元素以及一个 view 的根元素。这个 view 元素就是你没有使用Data Binding的layout文件的根元素

```xml
<layout xmlns:android="http://schemas.android.com/apk/res/android" >

    <data>

        <import
            alias="myUser"
            type="com.sss.demo.bean.User"/>

        <variable
            name="user"
            type="myUser"/>

        <variable
            name="name"
            type="String"/>

    </data>


    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical">


        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{name}"/>

        ...
    </LinearLayout>

</layout>
```

###Bean对象

自定义一个User实体

```java
public class User {
    private String name;
    private String age;

    public User() {
    }

    public User(String name, String age) {
        this.name = name;
        this.age = age;
    }
   ...
   
}
```


###调用
使用databinding，每个布局都会对应生成一个ViewDataBinding类，
类中包括可以传到页面的参数，以及各个控件的id，可以直接操作

activity中 ,把this.setContentView(R.layout.layout_name)替换成DataBindingUtil.setContentView(this, R.layout.layout_name);

```java
mainBinding = DataBindingUtil.setContentView(this, R.layout.activity_main);

```
向View中添加数据

```java
mainBinding.setName("name");

```

给对应控件添加事件

```java
mainBinding.button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                showToast("clicked button");
                adapter.setUser(new User("点击", "1"));
            }
    });
    
```
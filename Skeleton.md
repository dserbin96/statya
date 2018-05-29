При разработке приложений мы сталкиваемся с такой проблемой как отображение загружающихся данных. Как правило используют *ProgressDialog*, но начиная с *API 26*, класс объявлен устаревшим. В последнее время стало популярным использовать анимацию *skeleton* и этому есть пример, приложение Facebook. Существует большое количество библиотек, реализующих эту анимацию. Но в рамках этой статьи, я бы хотел рассказать о библиотеке – *Skeleton Android*.

# Анимация Skeleton.

**Скелетная анимация** – это технология анимации, в которой объект представлен двумя частями: поверхностной представление (что называется кожа или сетка) и совокупность взаимосвязанных частей (так называемый скелет). Скелетная анимация часто используется в разработке, например при создание компьютерных игр. Так же ее можно применить и в *Android* разработке.  Одним из таких примеров является применение *Skeleton Android*.
 

![Пример1](https://drive.google.com/uc?export=view&id=11UlrtcxfoRQy3dpiQe__bvMns8hvypmv)

# Сравнение с другими библиотеками.

Библиотека *Skeleton Android*, схожа с *shimmer-ом* приложения *facebook*. Однако *skeleton* имеет ряд преимуществ перед *shimmer-ом*. Одно из главных преимуществ, анимация. Анимация происходит поверх отображаемого *view*, как будто "кожа", а само *view* является непосредственно "скелетом". В итоге мы видим плавный переход из состояния загрузки. Если выбирать тип *gradient* она будто "пленкой" снимается с *view*, взять тип *alpha*, *view* будто всплывает. Еще одним преимуществом является встроенный адаптер для *RecyclerView*. Не каждая библиотека может похвастаться такой важной особенностью, обычно если встречается подобная библиотека, приходится самому создавать адаптер и как то управляться с анимацией, к сожалению не всегда получается это сделать. Например взять *shimmer facebook*, данная библиотека довольно красиво выглядит, однако она не предназначена для работы с *RecyclerView*. Можно попробовать ее внедрить во *ViewHolder* и на первый взгляд работать она будет нормально, но если проскролить вниз, мы увидим, как анимация в видимых элементах останавливается, что не очень красиво выглядит. Можно конечно подключить дополнительную библиотеку для работы конкретно с *RecyclerView*, то есть готовый адаптер от которого нужно только унаследоваться и дальше продолжить работать. Но зачем подключать дополнительную библиотеку чтобы работать и с *view* и *RecyclerView*, если можно просто подключить универсальную библиотеку.

# Добавление в проект.
 ~~~ gradle
repositories {
    ...
    maven { url "https://jitpack.io" } 
}

dependencies {
    ...
    implementation 'com.github.rasoulmiri:Skeleton:v1.0.9' 
}
~~~~

# SkeletonAndroid в xml файле.
 ~~~ xml
    <io.rmiri.skeleton.SkeletonGroup xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:Skeleton="http://schemas.android.com/apk/res-auto"
        android:id="@+id/skeletoneGroup"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <io.rmiri.skeleton.SkeletonView
            android:layout_width="match_parent"
            android:layout_height="wrap_content">            
            <!--...-->         
        </io.rmiri.skeleton.SkeletonView>

        <io.rmiri.skeleton.SkeletonView
            android:layout_width="match_parent"
            android:layout_height="wrap_content">   
            <!--...-->       
        </io.rmiri.skeleton.SkeletonView>
    </io.rmiri.skeleton.SkeletonGroup>
~~~

**SkeletonGroup** – контейнер, который включает в себя все используемые для этой анимации *views*. 
Ниже приведен неполный список атрибутов *SkeletonGroup*:
* *SK_animationAutoStart:*  если значение *true* то анимация будет происходить автоматически, *false* будет происходить при вызове метода *startAnimation* (по умолчанию *true*)
* *SK_animationDuration:* время анимации (по умолчанию 1000 миллисекунд)
* *SK_animationDirection:* как будет двигаться линия в анимации *RTL,LTR,BTT,TTB* (по умолчанию *LTR*)
* *SK_animationNormalType:* какая анимация будет происходит во время ее выполения: *none,alpha,gradient* ( по умолчанию *gradient*)
* *SK_animationFinishType:* какая анимация будет происходит в конце ее выполения: *none,alpha,gradient* ( по умолчанию *gradient*)

Пример использования *SK_animationFinishType="gradient"* и *SK_animationNormalType="alpha"*:

![альфаГрадиент1](https://drive.google.com/uc?export=view&id=1oxiUfjPvhngwoefKbejjTMrYPx13Eb1j)

 ~~~ xml
<io.rmiri.skeleton.SkeletonGroup
    android:id="@+id/skeletonGroup"
    Skeleton:SK_animationFinishType="gradient"
    Skeleton:SK_animationNormalType="alpha"
    Skeleton:SK_backgroundMainColor="@android:color/transparent"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">
~~~

* *SK_backgroundMainColor:* background всего *SkeletonGroup*
* *SK_BackgroundViewsColor:* background *SkeletonViews* внутри *SkeletonGroup*
* *SK_highLightColor:* цвет линии в анимации

**SkeletonView** – обертка над *view*. Он также имеет собственные атрибуты:
* *SK_shapeType:* тип анимации *skeleton* для конкретного *view: rect, oval,text* ( по умолчанию *rect*)
* *SK_textLineNumber:* количество линий отображаемых при типе *text* (поумолчанию 3)
* *SK_textLineLastWidth:* ширина последней отображаемой строчки при типе text: *full, threeQuarters, half, quarter* (по умолчанию *threeQuarters*)
* *SK_textLineHeight:* высота линий при типе text (по умолчанию 24dp)
* *SK_textLineSpaceVertical:* расстояние между линиями при типе text (по умолчанию threeQuarters 4dp)

Пример испоьзования *SK_shapeType="oval"* и *SK_shapeType="text"*:

![текстКруг1](https://drive.google.com/uc?export=view&id=1kapSlh_WuNgA_ByIZ7-iRFu0rllkVEuc)

 ~~~ xml
        <io.rmiri.skeleton.SkeletonView
            android:id="@+id/skeletonView"
            Skeleton:SK_cornerRadius="0dp"
            Skeleton:SK_padding="0dp"
            Skeleton:SK_shapeType="oval"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content">

            <de.hdodenhof.circleimageview.CircleImageView
                android:layout_width="100dp"
                android:layout_height="100dp"
                android:scaleType="centerCrop"
                android:src="@drawable/astra" />
        </io.rmiri.skeleton.SkeletonView>

        <io.rmiri.skeleton.SkeletonView
            Skeleton:SK_cornerRadius="@dimen/radius"
            Skeleton:SK_shapeType="text"
            Skeleton:SK_textLineHeight="@dimen/margin"
            Skeleton:SK_textLineLastWidth="threeQuarters"
            Skeleton:SK_textLineNumber="3"
            Skeleton:SK_textLineSpaceVertical="@dimen/line_vertical"
            Skeleton:layout_constraintBottom_toBottomOf="parent"
            Skeleton:layout_constraintEnd_toEndOf="parent"
            Skeleton:layout_constraintStart_toEndOf="@+id/skeletonView"
            Skeleton:layout_constraintTop_toTopOf="parent"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_margin="@dimen/margin_small"
            android:gravity="center">

            <TextView
                android:id="@+id/tvText"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:textAlignment="center"
                android:textColor="@android:color/black"
                android:textSize="@dimen/textSize"
                tools:text="@string/description" />
        </io.rmiri.skeleton.SkeletonView>
~~~
# Класс SkeletonGroup.
 Класс *SkeletonGroup* мы использовали в *xml* файле, он содержит следующие методы:
* *startAnimation()* – запуск анимации
* *finishAnimation()* – завершение анимации
* *setAutoPlay(true)* – автоматический запуск анимации
* *setShowSkeleton(true)* – включение анимации

Также *SkeletonGroup* содержит свой *listener*
~~~ kotlin
skeletoneGroup.setSkeletonListener(object : SkeletonGroup.SkeletonListener { 
  override fun onFinishAnimation() {       
        //... 
  }    

  override fun onStartAnimation() {        
        //...    
  }
})

~~~

# Применение Skeleton в RecyclerView.
В версии *v1.0.9* появилась возможность применять данную анимацию в *RecyclerView*. Для этого необходимо создать свой собственный адаптер наследуясь от *AdapterSkeleton*, код будет выглядеть следующим образом:
 ~~~ kotlin 
class MyAndroidSkeletonAdapter() : AdapterSkeleton<ExampleViewModel, ViewHolder>() {

    constructor(context: Context,
                recyclerView: RecyclerView,
                list:List<ExampleViewModel>,
                listener: IsCanSetAdapterListener) : this() {
        this.context = context
        this.items = list
        this.isCanSetAdapterListener = listener

        measureHeightRecyclerViewAndItem(recyclerView,R.layout.item_skeleton)
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val view = LayoutInflater.from(parent.context).inflate(R.layout.item_skeleton,
                parent, false)
        return ViewHolder(view)
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        if(this.items.isNotEmpty()){
            holder.bind(this.items[position])
        }
    }

    inner class ViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
        fun bind(model: ExampleViewModel) {
            with(itemView) {
                if (skeletonConfig.isSkeletonIsOn) {
                    return
                } else {
                    skeletoneGroup.setShowSkeleton(false)
                    skeletoneGroup.finishAnimation()
                }
                Glide.with(itemView).load(model.imagePath).into(image)
                text.text = model.title
            }
        }
    }
}
~~~


Интерфейс *isCanSetAdapterListener* – для создания анимации *SkeltonAdapter* должен знать размеры элмента внутри *RecyclerView*, после определения размера *listener* позволяет нам сетить адаптер в *RecyclerView*.
 ~~~java
public interface IsCanSetAdapterListener {
    void isCanSet();
}
~~~
Метод *measureHeightRecyclerViewAndItem* определяет состояние *RecyclerView* для использования анимации, передает параметры в *SkeletonConfig*. Класс *SkeletonConfig* предназначен для работы c *RecyclerView*, его метод *isSkeletonIsOn* необходим для завершения анимации.

![адаптерLib1](https://drive.google.com/uc?export=view&id=1FIoqyFKTa__UfaonQmki2fyr23qIyja1)

# Почему же все-таки SkeletonAndroid?
В чем-же преимущества данной библиотеки? Во первых сама анимация, при завершение которой осуществляется плавный переход из состояния загрузки во *View* которое должно отображаться, во вторых анимация позволяет работать с *RecyclerView*, что является огромным преимуществом по сравнению с другими подобными библиотеками. В третьих все элементы поделены на разные части, что позаоляет применять различную анимацию к элементам, в отличие от *shimmer-а* facebook. Подводя итог хочу сказать, что несмотря на небольшое количество звезд библиотека довольно мощная хоть и небольшая.



Летом 2018 года на Google I/O была представлена новая версия constraint-layout – 2.0. Библиотека находится в alifa, однако успела уже о себе заявить. В новом дополнении появился новый layout – MotionLayout, который наследуется от ConstraintLayout и предназначен для анимаций. В своей статье я хоте бы показать, как можно будет применить layout уже на готовых проектах.

# Добавление в проект.

MotionLayout включен в constraint-layout 2.0 для добавления нужно прописать в gradle: 

~~~ gradle
dependencies {
    implementation 'com.android.support.constraint:constraint-layout:2.0.0-alpha2'
}
 ~~~

Однако еще раз повторюсь, библиотека находится на данный момент в alifa поэтому в release добавлять ее нельзя.

# Принцип работы.

Для создания анимации нам понадобиться два xml файла, один с версткой экрана, второй с анимацией.

В первом xml файле нужно сверстать экран, при этом рутовым Layout-ом для него будет уже не ConstraintLayout, а MotionLayout. Также в атрибуте app:layoutDescription пропишем второй xml файл в котором содержится анимация.

Xml файл с версткой экрана:

 ~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.motion.MotionLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:layoutDescription="@xml/transition_simple_example"
    tools:context=".example.simple.SimpleActivity">

    <View
        android:id="@+id/vSimple"
        android:layout_width="0dp"
        android:layout_height="100dp"
        android:background="@android:color/black"/>

</android.support.constraint.motion.MotionLayout>
 ~~~

В нем содержится одна view черного цвета. При этом можно заметить, что в нем нет атрибутов указывающих на расположение элемента внутри контейнера, так как это не обязательно ведь за расположение элементов отвечает второй xml файл прописанный в MotionLayout
* *app:layoutDescription="@xml/transition_simple_example"*
    
Файл с анимацией расположен в папке xml ресурсов android.

 ~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MotionScene xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:motion="http://schemas.android.com/apk/res-auto">

    <Transition
        motion:constraintSetEnd="@layout/activity_simple_end"
        motion:constraintSetStart="@layout/activity_simple_start">

        <OnSwipe
            motion:dragDirection="dragUp"
            motion:touchAnchorId="@id/vSimple"
            motion:touchAnchorSide="bottom" />

    </Transition>

</MotionScene>
 ~~~

Элементы используемые в анимации:
* *MotionScene* – контейнер внутри которого прописываются анимация.
* *Transition* – элемент внутри которого прописывается переход от одного состояния в другое
* *OnSwipe* – элемент который поддерживает сенсорную обработку
Атрибуты используемые внутри элементов:
* *motion:constraintSetStart* – начальное состояние в анимации, внутри него может прописываться как layout, так и id для ConstraintSet(о нем поговорим чуть позже).
* *motion:constraintSetEnd* – аналогично предыдущему методу, но описывает конечное состояние в анимации.
* *motion:dragDirection* – в какую сторону идет перемещение (dragUp / dragDown | dragLeft / dragRight)
* *motion:touchAnchorId* – id элемента над которым происходит swipe
* *motion:touchAnchorSide* – сторона элемента которую нужно свайпнуть (top / left / right / bottom)

<p align="center">
  <img src="https://drive.google.com//uc?export=view&id=1d35mCSkVzAH99y0u2QZUCpq0zjUKJzzO"/>
 </p>

# ConstraintSet.
Теперь давайте посмотрим, как можно применить анимацию на экране с авторизацией.

<p align="center">
  <img src="https://drive.google.com//uc?export=view&id=1bfYk9ENxZzLZWPNM5Koyn2xWJVMJ5UKy"/>
 </p>

 ~~~xml
<Transition
    motion:constraintSetEnd="@id/end"
    motion:constraintSetStart="@id/start"
    motion:duration="1000">

    <OnClick
        motion:mode="transitionToEnd"
        motion:target="@id/btnSignInTest" />

</Transition>

<ConstraintSet android:id="@+id/start">

    <Constraint
        android:id="@id/btnSignInTest"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="8dp"
        motion:layout_constraintBottom_toBottomOf="parent"
        motion:layout_constraintEnd_toEndOf="parent"
        motion:layout_constraintStart_toStartOf="parent"
        motion:layout_constraintTop_toTopOf="parent" />

    <Constraint
        android:id="@id/metLogin"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginLeft="@dimen/big_spacing"
        android:layout_marginRight="@dimen/big_spacing"
        android:alpha="0"
       
        android:visibility="invisible"
        motion:layout_constraintBottom_toTopOf="@+id/metPassword"
        motion:layout_constraintLeft_toLeftOf="parent"
        motion:layout_constraintRight_toRightOf="parent"
        motion:layout_constraintTop_toTopOf="parent"
        motion:layout_constraintVertical_chainStyle="packed"
        motion:met_floatingLabel="highlight" />

    <Constraint
        android:id="@id/idTooglePasswordInputType"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginEnd="@dimen/big_spacing"
        android:layout_marginRight="@dimen/big_spacing"
        android:alpha="0"
        motion:layout_constraintBottom_toBottomOf="@+id/metPassword"
        motion:layout_constraintRight_toRightOf="parent"
        motion:layout_constraintTop_toTopOf="@+id/metPassword"/>

    <Constraint
        android:id="@id/metPassword"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginLeft="@dimen/big_spacing"
        android:layout_marginBottom="8dp"
        android:layout_weight="1"
        android:alpha="0"
        android:visibility="invisible"
        motion:layout_constraintBottom_toBottomOf="parent"
        motion:layout_constraintHorizontal_chainStyle="packed"
        motion:layout_constraintLeft_toLeftOf="parent"
        motion:layout_constraintRight_toLeftOf="@+id/idTooglePasswordInputType"
        motion:layout_constraintTop_toBottomOf="@id/metLogin"
        motion:layout_constraintVertical_chainStyle="packed"
        motion:met_floatingLabel="highlight" />

    <Constraint
        android:id="@id/idArrow"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginEnd="8dp"
        android:rotation="0"
        motion:layout_constraintBottom_toBottomOf="@+id/btnSignInTest"
        motion:layout_constraintEnd_toStartOf="@+id/btnSignInTest"
        motion:layout_constraintTop_toTopOf="@+id/btnSignInTest"
        motion:srcCompat="@drawable/ic_keyboard_arrow_right_black_24dp" />
</ConstraintSet>

<ConstraintSet android:id="@+id/end">

    <Constraint
        android:id="@id/btnSignInTest"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="8dp"
        motion:layout_constraintEnd_toEndOf="parent"
        motion:layout_constraintStart_toStartOf="parent"
        motion:layout_constraintTop_toBottomOf="@+id/metPassword" />

    <Constraint
        android:id="@id/metLogin"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginLeft="@dimen/big_spacing"
        android:layout_marginRight="@dimen/big_spacing"
        android:alpha="1"
        motion:layout_constraintBottom_toTopOf="@+id/metPassword"
        motion:layout_constraintLeft_toLeftOf="parent"
        motion:layout_constraintRight_toRightOf="parent"
        motion:layout_constraintTop_toTopOf="parent"
        motion:layout_constraintVertical_chainStyle="packed"
        motion:met_floatingLabel="highlight" />

    <Constraint
        android:id="@id/idTooglePasswordInputType"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginEnd="@dimen/big_spacing"
        android:layout_marginRight="@dimen/big_spacing"
        android:alpha="1"
        motion:layout_constraintBottom_toBottomOf="@+id/metPassword"
        motion:layout_constraintRight_toRightOf="parent"
        motion:layout_constraintTop_toTopOf="@+id/metPassword"/>

    <Constraint
        android:id="@id/metPassword"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginLeft="@dimen/big_spacing"
        android:layout_marginBottom="8dp"
        android:layout_weight="1"
        android:alpha="1"
        motion:layout_constraintBottom_toBottomOf="parent"
        motion:layout_constraintHorizontal_chainStyle="packed"
        motion:layout_constraintLeft_toLeftOf="parent"
           motion:layout_constraintRight_toLeftOf="@+id/idTooglePasswordInputType"
        motion:layout_constraintTop_toBottomOf="@+id/metLogin"
        motion:layout_constraintVertical_chainStyle="packed"
        motion:met_floatingLabel="highlight" />

    <Constraint
        android:id="@id/idArrow"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="8dp"
        android:rotation="90"
        motion:layout_constraintBottom_toTopOf="@+id/metLogin"
        motion:layout_constraintEnd_toEndOf="@+id/metLogin"
        motion:layout_constraintStart_toStartOf="@+id/metLogin"/>
</ConstraintSet>
 ~~~

Здесь в MotionScene вместе с Transition прописывается ConstraintSet. Внутри него заключены Constraint-ы. ConstraintSet описывает набор ограничений, который будет принимать Constraint в одном из состояний (начальном или конечном). Constraint описывает ограничение для объекта ConstraintLayout указанного с помощью id и другие атрибуты, связанные с представлением. А также можно заметить некоторые атрибуты прописанные внутри Constraint: rotation, alpha, visibility. Эти атрибуты принимают значения, которые описаны в определенном состоянии. Кроме них используются и другие стандартные атрибуты:elevation,rotation[X/Y],translation[X/Y/Z],scaleX/Y.
Атрибуты MotonLayout
В самом MotionLayout есть атрибуты :
* *app:layoutDescription* - указывает на файл MotionScene
* *app:applyMotionScene* - применяете или нет MotionScene [по умолчанию=true]
* *app:showPaths* - отображать или не отображать пути движения [по умолчанию=false].
* *app:progress* - укажите шаг перехода от 0 к 1
* *app:currentState* - использование определенного набора ограничений

# MotionLayout и DrawerLayout.

MotionLayout отлично взаимодействует и с другими layout-ми, например с DrawerLayout.

<p align="center">
  <img src="https://drive.google.com//uc?export=view&id=18h3TjuPyqHV8fNXKsFte7IhvpZKuyHCc"/>
 </p>

Для создания подобной анимации нужно создать свой собственный layout унаследованный от MotionLayout и расширенный с помощью  DrawerLayout.DrawerListener.

 ~~~kotlin
class DrawerContent @JvmOverloads constructor(context: Context, attr: AttributeSet? = null, defStyleAttr: Int = 0) :
        MotionLayout(context, attr, defStyleAttr), DrawerLayout.DrawerListener {

    override fun onDrawerStateChanged(newState: Int) {}

    override fun onDrawerSlide(drawerView: View, slideOffset: Float) {
        progress = slideOffset
    }

    override fun onDrawerClosed(drawerView: View) {}

    override fun onDrawerOpened(drawerView: View) {}

    override fun onAttachedToWindow() {
        super.onAttachedToWindow()

        (parent as? DrawerLayout)?.addDrawerListener(this)
    }
}
 ~~~

Здесь используется ключевой метод setProgress(float pos), с помощь него MotionLayout понимает какой прогресс имеет анимация. MotionScene имеет такой формат:

 ~~~kotlin
<Transition
    motion:constraintSetEnd="@id/end"
    motion:constraintSetStart="@id/start"/>

<ConstraintSet android:id="@id/end">
  <!—Элементы используемые в анимации-->
</ConstraintSet>

<ConstraintSet android:id="@id/start">
    <!—Элементы используемые в анимации-->
</ConstraintSet>
 ~~~

Можно заметить, что внутри Transition кроме атрибутов ничего нет. Нам не нужно нажимать на кнопку или делать свайп для включения анимации, за ходом анимации теперь отвечает setProgress(float pos).

# OnClick.

Кроме onSwipe и setProgress воспроизводить анимацию можно с помощью onClick.

Анимация выглядит примерно так:
<p align="center">
  <img src="https://drive.google.com//uc?export=view&id=1MME0BKHxk-VsmfF3UBsqUsZJsopfqOOE"/>
 </p>

 ~~~kotlin
<Transition
    motion:constraintSetEnd="@id/end"
    motion:constraintSetStart="@id/start"
    motion:duration="1000">
    <OnClick
        motion:mode="transitionToEnd"
        motion:target="@id/text" />

</Transition>

<ConstraintSet android:id="@+id/start">
        <!—Остальные элементы-->
</ConstraintSet>

<ConstraintSet android:id="@+id/end">
    <!—Остальные элементы-->
</ConstraintSet>
 ~~~

В этом коде используется onClick, он позволяет реагировать на нажатие кнопки. В нем используются следующие атрибуты:
* *motion:mode* – Направление кнопки для перемещения анимации  transitionToEnd, toggle, transitionToStart, jumpToEnd, jumpToStart
* *motion:target* – какой объект реагирует на нажатие

# Промежуточное состояние MotionLayout.
Кроме начального и конечного состояния MotionLayout есть промежуточное состояние. Оно прописывается в Transition

 ~~~kotlin
<Transition
    motion:constraintSetEnd="@id/end"
    motion:constraintSetStart="@id/start"
    motion:duration="5000"
    motion:interpolator="linear">

    <OnSwipe
        motion:dragDirection="dragUp"
        motion:touchAnchorId="@id/tvBeender"
        motion:touchAnchorSide="top" />

    <KeyFrameSet>
        <KeyPosition
            motion:framePosition="25"
            motion:keyPositionType="pathRelative"
            motion:percentX="1.75"
            motion:percentY="-0.3"
            motion:target="@id/ivGoogle" />
        <KeyPosition
            motion:framePosition="50"
            motion:keyPositionType="pathRelative"
            motion:percentX="1.25"
            motion:percentY="0.3"
            motion:target="@id/ivGoogle" />
        <KeyPosition
            motion:framePosition="75"
            motion:keyPositionType="pathRelative"
            motion:percentX="0.1"
            motion:percentY="0.3"
            motion:target="@id/ivGoogle" />

        <KeyPosition
            motion:framePosition="25"
            motion:keyPositionType="pathRelative"
            motion:percentX="1.75"
            motion:percentY="-0.3"
            motion:target="@id/ivFacebook" />
        <KeyPosition
            motion:framePosition="50"
            motion:keyPositionType="pathRelative"
            motion:percentX="1.25"
            motion:percentY="0.3"
            motion:target="@id/ivFacebook" />
        <KeyPosition
            motion:framePosition="75"
            motion:keyPositionType="pathRelative"
            motion:percentX="0.1"
            motion:percentY="0.3"
            motion:target="@id/ivFacebook" />
    </KeyFrameSet>

</Transition>

<ConstraintSet android:id="@+id/start">
      <!--Остальные элементы-->
    <Constraint
        android:id="@id/ivFacebook"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        motion:layout_constraintBottom_toBottomOf="parent"
        motion:layout_constraintEnd_toEndOf="parent"
        motion:layout_constraintTop_toTopOf="parent" >
        <CustomAttribute
            motion:attributeName="backgroundColor"
            motion:customColorValue="@android:color/black"/>
    </Constraint>
</ConstraintSet>

<ConstraintSet android:id="@+id/end">
    <!--Остальные элементы-->

    <Constraint
        android:id="@id/ivFacebook"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="8dp"
        android:layout_marginEnd="8dp"
        android:layout_marginStart="8dp"
        android:layout_marginTop="8dp"
        motion:layout_constraintBottom_toBottomOf="parent"
        motion:layout_constraintEnd_toEndOf="parent"
        motion:layout_constraintStart_toStartOf="parent"
        motion:layout_constraintTop_toBottomOf="@+id/ivGoogle" >
        <CustomAttribute
            motion:attributeName="backgroundColor"
            motion:customColorValue="@color/colorPrimary"/>
    </Constraint>

</ConstraintSet>
 ~~~

О каждом элементе внутри Transition по подробнее:
* *KeyFrameSet* – описывает набор ключевых значений которые влияют на анимацию
* *KeyPosition* – положение макета во время анимации

Атрибуты которые используют KeyFrameSet, KeyPosition:

* *motion:framePosition* – прогресс анимации от 0 до 100
* *motion:keyPositionType* – задает тип системы координат parentRelative, deltaRelative, pathRelative
* *motion:percent[X/Y]* – координата (x,y) положения
* *motion:target* – к какому элементу применяется кадр

Еще добавился новый элемент в Constraint – CustomAttribute. Он предназначен для применения кастомных атрибутов, в данном случае используется backgroundColor. Он содержит свои собственные атрибуты

* *motion:attributeName* – указывается какой атрибут будет применен
* *motion: customColorValue/ customIntegerValue/ customFloatValue/ customStringValue/ customDimension/ customBoolean* – какое значение атрибута используется.
Такая анимация будет выглядеть так (линиями указана траектория):

<p align="center">
  <img src="https://drive.google.com//uc?export=view&id=1Y88hWi1zmNrxhyQmGZOKN6VxV0QAA8Gm"/>
 </p>

# Заключение.
В данной статье рассказана только часть возможностей MotionLayout, чтобы использовать такую анимацию с умом нужна фантазия. Библиотека очень мощная, она сама по себе во многом облегчает жизнь разработчику ведь теперь применяя ее мы можем убрать значительную часть кода в java и перенести ее в xml, что само по себе приятно. Я постарался показать на практике большинство плюшек данного layout-а, но библиотека слишком большая и многое здесь не написано. Остается ждать стабильной версии библиотеки. При написании я опирался на 
https://medium.com/google-developers/introduction-to-motionlayout-part-i-29208674b10d, https://developer.android.com/reference/android/support/constraint/motion/MotionLayout.
Пример приложения: https://github.com/dserbin96/MotionLayoutExample.

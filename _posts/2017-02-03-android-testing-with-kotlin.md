---
id: 509
title: Android Testing with Kotlin
date: 2017-02-03T19:15:58+00:00
author: fcejas
layout: post
guid: http://fernandocejas.com/?p=509
permalink: /2017/02/03/android-testing-with-kotlin/
categories:
  - Android
  - Development
  - Kotlin
  - Software Architecture
  - Testing
tags:
  - android
  - androiddev
  - architecture
  - clean
  - clean architecture
  - developer
  - developers
  - development
  - java
  - kotlin
  - mobile
  - mobile dev
  - programmer
  - programming
  - reactive programming
  - rxjava
  - testing
---
<span style="color: #333399;"><strong>This is not a new topic actually</strong></span>, especially since <a href="https://kotlinlang.org" target="_blank">Kotlin</a> is gaining terrain in the world of programming languages in general, and especially on <span style="color: #333399;"><strong>Android</strong></span>. Also, I&#8217;m not going to get into details on <span style="color: #333399;"><strong>what Kotlin offers</strong></span> since <span style="color: #000080;"><em>there is people out there doing a great job</em></span> (especial mention to my friend <a href="http://antonioleiva.com" target="_blank">Antonio Leiva</a>).

## Kotlin Programming Language

Before starting I will just mention and give a <span style="color: #000080;"><strong>quick summary of the main benefits of this &#8220;young?&#8221; and modern programming language</strong></span>:

  * **<span style="color: #000080;">Kotlin is concise.</span>** The less code you write, the fewer mistakes you make.
  * **<span style="color: #000080;">Kotlin is expressive.</span>** You can express whatever you want in a shorter way (Java is verbose).
  * **<span style="color: #000080;">Kotlin is pragmatic.</span>** Straight to the point without a lot of wiring.
  * <span style="color: #000080;"><strong>Kotlin is android-friendly.</strong></span> As we can see in this article ;).
  * <span style="color: #000080;"><strong>Kotlin is type-safe.</strong></span> Remember the billion dollars mistake?.
  * **<span style="color: #000080;">Kotlin is functional.</span>** Functions and properties are first-class citizens.
  * <span style="color: #000080;"><strong>Kotlin is friendly.</strong></span> Interoperability between Kotlin and Java works almost perfect.

## Why Kotlin in Tests?

_<span style="color: #000080;">We have an android codebase</span>_ in our old and lovely? <span style="color: #000080;"><strong>Java</strong></span> and we would love to introduce this awesome language gradually, so <span style="color: #000080;"><strong>why not starting with tests?</strong></span> This way we can give it a try without affecting our main application under any circumstances, <span style="color: #000080;"><strong>and at the same time we get the excitement of a modern and already mature language,</strong></span> plus some training for us and our team in preparation for the big change. Sounds good right? <span style="color: #000080;"><strong>Let&#8217;s write some code then&#8230;</strong></span>

## Getting our hands dirty

<span style="color: #000080;"><strong>Basically, the idea is to showcase how we can test our android applications using Kotlin</strong></span>, so as a first step we need to setup and prepare our environment by <span style="color: #000080;"><strong>adding Kotlin dependencies</strong></span> in our <span style="color: #000080;"><em>build.gradle</em></span> file:

<pre class="font-size:13 lang:java decode:true " title="build.gradle">buildscript {
  repositories {
    mavenCentral()
    jcenter()
  }
  dependencies {
    classpath 'org.jetbrains.kotlin:kotlin-gradle-plugin:1.0.5-2'
  }
}

apply plugin: 'com.android.application'
apply plugin: 'kotlin-android'

...

dependencies {
  ...
  compile "org.jetbrains.kotlin:kotlin-stdlib:1.0.6"

  ...
  testCompile 'org.jetbrains.kotlin:kotlin-stdlib:1.0.6'
  testCompile 'org.jetbrains.kotlin:kotlin-test-junit:1.0.6'
  testCompile "com.nhaarman:mockito-kotlin:1.1.0"
  testCompile 'org.amshove.kluent:kluent:1.14'
}</pre>

<span style="color: #000080;"><strong>Now we need to set the dedicated directories for tests written in Kotlin</strong></span>, this is done in our **<span style="color: #000080;">sourceSets</span>** section:

<pre class="font-size:13 lang:java decode:true" title="build.gradle">android {
  ...
  sourceSets {
    test.java.srcDirs += 'src/test/kotlin'
    androidTest.java.srcDirs += 'src/androidTest/kotlin'
  }
  ...
}</pre>

<span style="color: #000080;"><strong>And as a third step we want to make sure, we do not allow accidentally (for now ;)) any Kotlin code in production by also adding this defensive lines:</strong></span>

<pre class="font-size:13 lang:java decode:true" title="build.gradle">afterEvaluate {
  android.sourceSets.all { sourceSet -&gt;
    if (!sourceSet.name.startsWith('test') || !sourceSet.name.startsWith('androidTest')) {
      sourceSet.kotlin.setSrcDirs([])
    }
  }
}</pre>

The <a href="https://github.com/android10/Android-KotlinInTests/blob/master/app/build.gradle" target="_blank">entire file</a> can be seen in the <a href="https://github.com/android10/Android-KotlinInTests" target="_blank">sample project on Github</a>.
  
<span style="color: #000080;"><strong>Now we are able to write tests in the same way as we would do in Java.</strong></span>

## JUnit Tests

What I only need here is <a href="http://junit.org/junit4/" target="_blank">JUnit</a>, <a href="https://github.com/nhaarman/mockito-kotlin" target="_blank">Mockito-kotlin</a> and <a href="https://github.com/MarkusAmshove/Kluent" target="_blank">Kluent</a> (a library with really cool assertion semantics).
  
Let&#8217;s see a simple test case for a class called <span style="color: #000080;"><strong>GetUserDetails.java</strong></span> which is a <span style="color: #000080;"><strong>UseCase</strong></span> (<a href="http://fernandocejas.com/2015/07/18/architecting-android-the-evolution/" target="_blank">from a Clean Architecture approach</a>) in my application:

<pre class="font-size:13 lang:java decode:true" title="GetUserDetails.kt">class GetUserDetailsTest {

    private val USER_ID = 123

    private lateinit var getUserDetails: GetUserDetails

    private val userRepository: UserRepository = mock()
    private val threadExecutor: ThreadExecutor = mock()
    private val postExecutionThread: PostExecutionThread = mock()

    @Before
    fun setUp() {
        getUserDetails = GetUserDetails(userRepository, threadExecutor, postExecutionThread)
    }

    @Test
    fun shouldGetUserDetails() {
        getUserDetails.buildUseCaseObservable(GetUserDetails.Params.forUser(USER_ID));

        verify(userRepository).user(USER_ID)
        verifyNoMoreInteractions(userRepository)
        verifyZeroInteractions(postExecutionThread)
        verifyZeroInteractions(threadExecutor)
    }
}</pre>

<span style="color: #000080;"><strong>Something to pay attention is that when we need to construct our subject under test (in the setup method), we must declare it</strong> </span>&#8220;<a href="https://kotlinlang.org/docs/reference/properties.html" target="_blank">lateinit</a>&#8220;, otherwise the compiler will complain, since properties must be initialized or be abstract. Here is another test example for a <span style="color: #000080;"><strong>Serializer.java</strong></span> class in the project, where you can see the <span style="color: #000080;"><strong>assertions</strong></span> mentioned above:

<pre class="font-size:13 lang:java decode:true" title="SerializerTest.kt">class SerializerTest {

    private val JSON_RESPONSE = "{\n \"id\": 1,\n " +
                                "\"cover_url\": \"http://www.android10.org/myapi/cover_1.jpg\",\n " +
                                "\"full_name\": \"Simon Hill\",\n " +
                                "\"description\": \"Curabitur gravida nisi at nibh. In hac habitasse " +
                                "platea dictumst. Aliquam augue quam, sollicitudin vitae, consectetuer " +
                                "eget, rutrum at, lorem.\\n\\nInteger tincidunt ante vel ipsum. " +
                                "Praesent blandit lacinia erat. Vestibulum sed magna at nunc commodo " +
                                "placerat.\\n\\nPraesent blandit. Nam nulla. Integer pede justo, " +
                                "lacinia eget, tincidunt eget, tempus vel, pede.\",\n " +
                                "\"followers\": 7484,\n " +
                                "\"email\": \"jcooper@babbleset.edu\"\n}"

    private var serializer = Serializer()

    @Test
    fun shouldSerialize() {
        val userEntityOne = serializer.deserialize(JSON_RESPONSE, UserEntity::class.java)
        val jsonString = serializer.serialize(userEntityOne, UserEntity::class.java)
        val userEntityTwo = serializer.deserialize(jsonString, UserEntity::class.java)

        userEntityOne.userId shouldEqual userEntityTwo.userId
        userEntityOne.fullname shouldEqual userEntityTwo.fullname
        userEntityOne.followers shouldEqual userEntityTwo.followers
    }

    @Test
    fun shouldDesearialize() {
        val userEntity = serializer.deserialize(JSON_RESPONSE, UserEntity::class.java)

        userEntity.userId shouldEqual 1
        userEntity.fullname shouldEqual "Simon Hill"
        userEntity.followers shouldEqual 7484
    }
}</pre>

## Robolectric (Integration?) Tests

<span style="color: #000080;"><strong>I created a test parent class (used for each test case) in order to encapsulate everything <a style="color: #000080;" href="http://robolectric.org/" target="_blank">Robolectic</a> related,</strong></span> thus, my tests do not depend directly on this framework. The idea is that any functionality or helper method is wrapped here (<span style="color: #000080;"><strong>this is a lesson learned from the past</strong></span> where I polluted all my code with Robolectric classes, so the process of migrating to a non-backward compatible newer version was very painful).

<pre class="font-size:13 lang:java decode:true" title="AndroidTest.kt">/**
 * Base class for Robolectric data layer tests.
 * Inherit from this class to create a test.
 */
@RunWith(RobolectricTestRunner::class)
@Config(constants = BuildConfig::class,
        application = AndroidTest.ApplicationStub::class,
        sdk = intArrayOf(21))
abstract class AndroidTest {

    fun context(): Context {
        return RuntimeEnvironment.application
    }

    fun cacheDir(): File {
        return context().cacheDir
    }

    internal class ApplicationStub : Application()
}</pre>

And here is an example of an <span style="color: #000080;"><strong>integration test</strong></span> that interacts with the android framework through our <span style="color: #000080;"><strong>AndroidTest.kt</strong></span> class:

<pre class="font-size:13 lang:java decode:true" title="FileManagerTest.kt">class FileManagerTest : AndroidTest() {

    private var fileManager = FileManager()

    @After
    fun tearDown() {
        fileManager.clearDirectory(cacheDir())
    }

    @Test
    fun shouldWriteToFile() {
        val fileToWrite = createDummyFile()
        val fileContent = "content"

        fileManager.writeToFile(fileToWrite, fileContent)

        fileToWrite.exists() shouldEqualTo true
    }

    @Test
    fun shouldHaveCorrectFileContent() {
        val fileToWrite = createDummyFile()
        val fileContent = "content\n"

        fileManager.writeToFile(fileToWrite, fileContent)
        val expectedContent = fileManager.readFileContent(fileToWrite)

        expectedContent shouldEqualTo fileContent
    }

    private fun createDummyFile(): File {
        val dummyFilePath = cacheDir().path + File.separator + "dummyFile"
        return File(dummyFilePath)
    }
}</pre>

## Espresso Acceptance (UI?) Tests

**<span style="color: #000080;">My choice here is <a style="color: #000080;" href="https://google.github.io/android-testing-support-library/docs/espresso/" target="_blank">Espresso</a> since it is backed by Google</span>** and from my perspective the most stable integration test framework nowadays. The same as with Robolectric,<span style="color: #000080;"><strong> I decided to create a little framework on top of it,</strong></span> let&#8217;s see how it works. All my tests depend on an <span style="color: #000080;"><strong>AcceptanceTest.kt</strong></span> class:

<pre class="font-size:13 lang:java decode:true" title="AcceptanceTest.kt">@LargeTest
@RunWith(AndroidJUnit4::class)
abstract class AcceptanceTest&lt;T : Activity&gt;(clazz: Class&lt;T&gt;) {

    @Rule @JvmField
    val testRule: ActivityTestRule&lt;T&gt; = IntentsTestRule(clazz)

    val checkThat: Matchers = Matchers()
    val events: Events = Events()
}</pre>

<span style="color: #000080;"><em>Things to keep an eye on here:</em></span>

  * <span style="color: #000080;"><strong>A test rule is needed within Espresso (<a style="color: #000080;" href="https://developer.android.com/reference/android/support/test/rule/ActivityTestRule.html" target="_blank">from the documentation</a>):</strong></span> This rule provides functional testing of a single activity. The activity under test will be launched before each test annotated with @Test and before methods annotated with @Before. It will be terminated after the test is completed and methods annotated with @After are finished. During the duration of the test you will be able to manipulate your Activity directly.
  * <span style="color: #000080;"><strong>We must annotate our &#8220;testRule&#8221; field with <a style="color: #000080;" href="https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-field/" target="_blank">@JvmField</a>:</strong></span> this is necessary to turn this Kotlin property into a JVM field that JUnit can interpret.
  * <span style="color: #000080;"><strong>Matchers class:</strong></span> A wrapper around Espresso checks.
  * <span style="color: #000080;"><strong>Events class:</strong></span> Another wrapper encapsulating Espresso events.

<pre class="font-size:13 lang:java decode:true" title="Matchers.kt">class Matchers {
    fun &lt;T : Activity&gt; nextOpenActivityIs(clazz: Class&lt;T&gt;) {
        intended(IntentMatchers.hasComponent(clazz.name))
    }

    fun viewIsVisibleAndContainsText(@StringRes stringResource: Int) {
        onView(withText(stringResource)).check(matches(withEffectiveVisibility(Visibility.VISIBLE)))
    }

    fun viewContainsText(@IdRes viewId: Int, @StringRes stringResource: Int) {
        onView(withId(viewId)).check(matches(withText(stringResource)))
    }
}</pre>

<pre class="font-size:13 lang:java decode:true" title="Events.kt">class Events {
    fun clickOnView(@IdRes viewId: Int) {
        onView(withId(viewId)).perform(click())
    }
}</pre>

<span style="color: #000080;"><strong>Last but not least, an example of the main activity of the project</strong></span>, which displays a view and also opens another activity when clicking on a button. The code is pretty simple but if you need a better understanding, <a href="https://github.com/android10/Android-KotlinInTests" target="_blank">you can browse the sample code on Github</a>.

<pre class="font-size:13 lang:java decode:true" title="MainActivityTest.kt">class MainActivityTest : AcceptanceTest&lt;MainActivity&gt;(MainActivity::class.java) {

    @Test
    fun shouldOpenHelloWorldScreen() {
        events.clickOnView(R.id.btn_hello_world)
        checkThat.nextOpenActivityIs(HelloWorldActivity::class.java)
    }

    @Test
    fun shouldDisplayAction() {
        events.clickOnView(R.id.fab)
        checkThat.viewIsVisibleAndContainsText(R.string.action)
    }
}</pre>

## Running our test battery

<span style="color: #000080;"><strong>There are neither problems nor especial configuration to run our tests from Android Studio/Intellij. </strong></span>I also added a couple of Gradle tasks in my root <a href="https://github.com/android10/Android-KotlinInTests/blob/master/build.gradle" target="_blank">build.gradle</a> file:

<pre class="font-size:13 lang:java decode:true" title="build.gradle">task runUnitTests(dependsOn: [':app:testDebugUnitTest']) {
  description 'Run all unit tests'
}

task runAcceptanceTests(dependsOn: [':app:connectedAndroidTest']) {
  description 'Run all acceptance tests.'
}</pre>

<span style="color: #000080;"><strong>Just type this from the command line:</strong></span>

<pre class="font-size:13 lang:java decode:true" title="command line">./gradlew runUnitTests
./gradlew runAcceptanceTests</pre>

## Wrapping up

<span style="color: #000080;"><strong>If at some point you were considering Kotlin, there are no longer excuses to use it in production.</strong></span> Tests are a good starting point to introduce it, plus this will help you to have a taste of what this language has to offer. <span style="color: #000080;"><strong>Of course as an extra ball you get all the benefits mentioned in this article and a nice training that will prepare you and your team for the big change.</strong></span>

## Repo

<a href="https://github.com/android10/Android-KotlinInTests" target="_blank">https://github.com/android10/Android-KotlinInTests</a>

## Resources

<a href="https://yalantis.com/blog/kotlin-vs-java-syntax/" target="_blank">https://yalantis.com/blog/kotlin-vs-java-syntax/</a>
  
[https://medium.com/@sergii/using-kotlin-for-tests-in-android](https://medium.com/@sergii/using-kotlin-for-tests-in-android-6d4a0c818776#.yllw2s8ew)
  
<a href="http://blog.greenhouseci.com/greenhouse/update/android-testing-with-kotlin/" target="_blank">http://blog.greenhouseci.com/greenhouse/update/android-testing-with-kotlin/</a>
  
<a href="http://hadihariri.com/2016/10/04/Mocking-Kotlin-With-Mockito/" target="_blank">http://hadihariri.com/2016/10/04/Mocking-Kotlin-With-Mockito/</a>
  
<a href="https://medium.com/@elye.project/befriending-kotlin-and-mockito-1c2e7b0ef791#.35tvarv9h" target="_blank">https://medium.com/@elye.project/befriending-kotlin-and-mockito</a>
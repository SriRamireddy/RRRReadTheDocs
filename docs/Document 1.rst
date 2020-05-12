========================
Android Kotlin Corotines
========================
==================
What is Coroutines
==================
A coroutine is a concurrency design pattern that you can use on Android to simplify code that executes asynchronously. Coroutines were added to Kotlin in version 1.3 and are based on established concepts from other languages.

On Android, coroutines help to solve two primary problems:

1. Manage long-running tasks that might otherwise block the main thread and cause your app to freeze.
2. Providing main-safety, or safely calling network or disk operations from the main thread.

This topic describes how you can use Kotlin coroutines to address these problems, enabling you to write cleaner and more concise app code.

====================
Coroutines in Kotlin
====================

On Android, it's essential to avoid blocking the main thread. The main thread is a single thread that handles all updates to the UI. It's also the thread that calls all click handlers and other UI callbacks. As such, it has to run smoothly to guarantee a great user experience.

For your app to display to the user without any visible pauses, the main thread has to update the screen every 16ms or more, which is about 60 frames per second. Many common tasks take longer than this, such as parsing large JSON datasets, writing data to a database, or fetching data from the network. Therefore, calling code like this from the main thread can cause the app to pause, stutter, or even freeze. And if you block the main thread for too long, the app may even crash and present an Application Not Responding dialog.


Add Dependency
##############
In build.gradle(Module:app) -> dependency block
:: 

 
   implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-core:1.3.5'
   implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-android:1.3.5'
   
Before onCreate initialize mutableList objects and API
######################################################
::
   
    val countryNameList : MutableList<String> = mutableListOf()
    var countryFlag : MutableList<String> = mutableListOf()

    val url : String = "https://corona.lmao.ninja/v2/countries"

    override fun onCreate(savedInstanceState: Bundle?) {
    
    //statements
    
    }

suspend function Syntax
################
::

   suspend fun getData()
   {
      // Get data from server 
   }

Suspend work flow
#################

.. figure::  corotines.gif
   :align:   center

   Suspend function work flow.
   
Write Code for getting data by using Coroutines
###############################################
In User defined fun we call suspend function by using coroutineScope for getting data from internet
::

   fun callCoroutines(){
        CoroutineScope(Dispatchers.IO).launch {
            getCountryWiseData()
        }
Understanding CoroutineScope
############################
In Kotlin, all coroutines run inside a CoroutineScope. A scope controls the lifetime of coroutines through its job. When you cancel the job of a scope, it cancels all coroutines started in that scope. On Android, you can use a scope to cancel all running coroutines when, for example, the user navigates away from an Activity or Fragment. Scopes also allow you to specify a default dispatcher. A dispatcher controls which thread runs a coroutine.

For coroutines started by the UI, it is typically correct to start them on **Dispatchers.Main** which is the main thread on Android. A coroutine started on Dispatchers.Main won't block the main thread while suspended. Since a ViewModel coroutine almost always updates the UI on the main thread, starting coroutines on the main thread saves you extra thread switches. A coroutine started on the Main thread can switch dispatchers any time after it's started. For example, it can use another dispatcher to parse a large JSON result off the main thread.

**Dispatchers.Default** — is used by all standard builders if no dispatcher or any other ContinuationInterceptor is specified in their context. It uses a common pool of shared background threads. This is an appropriate choice for compute-intensive coroutines that consume CPU resources.

**Dispatchers.IO** — uses a shared pool of on-demand created threads and is designed for offloading of IO-intensive blocking operations (like file I/O and blocking socket I/O).

**Dispatchers.Unconfined** — starts coroutine execution in the current call-frame until the first suspension, whereupon the coroutine builder function returns. The coroutine will later resume in whatever thread used by the corresponding suspending function, without confining it to any specific thread or pool. The Unconfined dispatcher should not normally be used in code.

Connect to Http
###############

::

   suspend fun getCountryWiseData() 
   {
     val url = URL(url)
     val httpsURLConnection : HttpsURLConnection = url.openConnection() as HttpsURLConnection
     val inputStream : InputStream = httpsURLConnection.inputStream
     val text = inputStream.bufferedReader().use(BufferedReader::readText)
     withContext(Dispatchers.Main)
     {
           val root  = JSONArray(text)
           for( i in 0..root.length()-1)
           {
              val po = root.getJSONObject(i)
              val countryName = po.getString("country").toString()
              val flagUrl = forFlag.getString("flag").toString()
              countryNameList.add(countryName)
              countryFlag.add(flagUrl)      
           }
        }
    }
      
=========================
Creating the RecyclerView
=========================

Add Recyclerview and Picasso Dependency
##############
In build.gradle(Module:app) -> dependency block
:: 

   implementation 'androidx.recyclerview:recyclerview:1.1.0'
   implementation 'com.squareup.picasso:picasso:2.71828'//for loading image from url

To create the RecyclerView, break the work into four parts:

Declare the RecyclerView in an activity layout and reference it in the activity Kotlin file.
Create a custom item XML layout for RecyclerView for its items.
Create the view holder for view items, connect the data source of the RecyclerView and handle the view logic by creating a RecyclerView Adapter.
Attach the adapter to the RecyclerView.
Step one should be familiar. Open up the activity_main.xml layout file and add the following as a child of the LinearLayout:

::

   <android.support.v7.widget.RecyclerView
   android:id="@+id/recyclerView"
   android:layout_width="match_parent"
   android:layout_height="match_parent"/>

Open MainActivity.kt and declare the following property at the top of the class:

::

  private lateinit var linearLayoutManager: LinearLayoutManager

In onCreate(), add the following lines after setContentView:

::
  
  linearLayoutManager = LinearLayoutManager(this)
  recyclerView.layoutManager = linearLayoutManager

Create **row.xml** file for design with LinearLayout horizantal orientation

Add the following XML elements **row** XML file:

::

     <ImageView
      android:id="@+id/country_flag"
      android:layout_width="0dp"
      android:layout_height="wrap_content"
      android:layout_weight="2"
      />

    <TextView
     android:id="@+id/country_name"
     android:layout_width="0dp"
     android:layout_height="wrap_content"
     android:layout_weight="8"
     />
     
=========================
Adapters for RecyclerView
=========================

Right-click on the first package, select **New ‣ Kotlin File ‣ Class**, name it **RecyclerAdapter** and select Class for Kind.

Make the class extend RecyclerView.Adapter as in the following:

::

  class RecyclerAdapter : RecyclerView.Adapter<RecyclerAdapter.PhotoHolder>()  {
  }

Android Studio will prompt you to import the RecyclerView class. Click on RecylerView and press **Alt-Enter** on a PC and choose **Import**. Since you’re extending a class that has required methods, Android Studio will underline your class declaration with a red squiggle.


.. figure::  https://koenig-media.raywenderlich.com/uploads/2019/02/implement_members-650x259.png
   :align:   center

Select all three methods and press OK to implement the suggested methods:

.. figure::  https://koenig-media.raywenderlich.com/uploads/2019/02/implement_members_dialog-413x500.png
   :align:   center
   

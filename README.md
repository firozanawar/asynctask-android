# asynctask-android
This project demonstrate the basic implementation of AsyncTask in android.

### AsyncTask Implementation:-

* Android AsyncTask is an abstract class provided by Android which gives us the liberty to perform heavy tasks in the background and keep the UI thread light thus making the application more responsive.
* AsyncTask allows you to perform background operations and publish results on the UI thread without having to manipulate threads and/or handlers. AsyncTasks should ideally be used for short operations (a few seconds at the most.)
* AsyncTask must be subclassed to be used. The subclass will override at least one method (doInBackground(Params...)) The parameters are the following AsyncTask <TypeOfVarArgParams, ProgressValue, ResultValue>.

It has 3 generic types parameters, called Params, Progress and Result, and 4 steps, called onPreExecute, doInBackground, onProgressUpdate and onPostExecute.

The three types used by an asynchronous task are the following:
* Params, the type of the parameters sent to the task upon execution.
* Progress, the type of the progress units published during the background computation.
* Result, the type of the result of the background computation.

When an asynchronous task is executed, the task goes through 4 steps:
* onPreExecute(), invoked on the UI thread before the task is executed. This step is normally used to setup the task, for instance by showing a progress bar in the user interface.
* doInBackground(Params...), invoked on the background thread immediately after onPreExecute() finishes executing. This step is used to perform background computation that can take a long time. The parameters of the asynchronous task are passed to this step. The result of the computation must be returned by this step and will be passed back to the last step. This step can also use 
* publishProgress(Progress...) to publish one or more units of progress. These values are published on the UI thread, in the onProgressUpdate(Progress...) step.
onProgressUpdate(Progress...), invoked on the UI thread after a call to publishProgress(Progress...). The timing of the execution is undefined. This method is used to display any form of progress in the user interface while the background computation is still executing. For instance, it can be used to animate a progress bar or show logs in a text field.
* onPostExecute(Result), invoked on the UI thread after the background computation finishes. The result of the background computation is passed to this step as a parameter.

### Cancelling a task:-
Use cancel(boolean). Check isCancelled() to check. Calling cancel() ensures that onPostExecute() is never invoked. But it does invoke the onCancelled() callback on the UI thread after doInBackground() returns.

private class DownloadFilesTask extends AsyncTask<URL, Integer, Long> {

     protected Long doInBackground(URL... urls) {
     
         int count = urls.length;
         
         long totalSize = 0;
         
         for (int i = 0; i < count; i++) {
         
             totalSize += Downloader.downloadFile(urls[i]);
             
             publishProgress((int) ((i / (float) count) * 100));
             
             // Escape early if cancel() is called
             
             if (isCancelled()) break;
             
         }
         
         return totalSize;
         
     }

     protected void onProgressUpdate(Integer... progress) {
         setProgressPercent(progress[0]);
     }

     protected void onPostExecute(Long result) {
         showDialog("Downloaded " + result + " bytes");
     }
 }

Execute the task using
new DownloadFilesTask().execute(url1, url2, url3);

### Note:-
* The AsyncTask instance must be created and invoked in the UI thread.
* The methods overridden in the AsyncTask class should never be called. They’re called automatically.
* AsyncTask can be called only once. Executing it again will throw an exception.

### Parallel execution of several AsyncTask:-
By default, AsyncTask tasks are executed sequence (for Android versions higher than Android 3.0). To run AsyncTasks in sequence use the executeOnExecutor() method specifying AsyncTask.THREAD_POOL_EXECUTOR as first parameter.

// Assume ImageLoader extends AsyncTask

ImageLoader imageLoader = new ImageLoader( imageView );

// Execute in parallel

imageLoader.executeOnExecutor( AsyncTask.THREAD_POOL_EXECUTOR, "http://url.com/image.png" );

The AsyncTask class has two static Executors that you can pass to executeOnExecutor():

* AsyncTask.THREAD_POOL_EXECUTOR – An Executor that can be used to execute tasks in parallel on its pool of threads.
* AsyncTask.SERIAL_EXECUTOR – An Executor that executes one task at a time serially (sequentially), hence thread-safe.


### Using AsyncTask:-
* It is intended for shorter task, lasting for few seconds
* Each instance of AsyncTask can be executed only once.
* Any AsyncTask is bound to a single activity.
* AsyncTask object don’t survive on configuration changes.

References:-
* https://developer.android.com/reference/android/os/AsyncTask.html
* https://www.journaldev.com/9708/android-asynctask-example-tutorial
* http://www.vogella.com/tutorials/AndroidBackgroundProcessing/article.html
* http://codetheory.in/android-asynctask/
* https://androidresearch.wordpress.com/2012/03/17/understanding-asynctask-once-and-forever/

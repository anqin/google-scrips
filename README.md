# Remove script for Gmail // 批量删除Gmail邮件

1. Go to https://script.google.com and create a new script
2. Copy the following codes to the script input
3. Click the save icon
4. Click the "Run" menu
5. Click the "Install" submenu entry and then click the "Run" botton
6. Wait a few days, it will delete 10,0000 unread messages each 10 minutes

``` javascript
function Intialize() {
  return;
}

function Install() {
  ScriptApp.newTrigger("removeUnUselessEmails")
           .timeBased().everyMinutes(10).create();

}

function Uninstall() {
  
  var triggers = ScriptApp.getScriptTriggers();
  for (var i=0; i<triggers.length; i++) {
    ScriptApp.deleteTrigger(triggers[i]);
  }
  
}

function getThreadsMatchingSearch(searchString){

  // Get all threads matching search string
  try{
    var threads = GmailApp.search(searchString);
  Logger.log("Found " + threads.length + " threads (conversations of emails) matching search string (" + searchString + "). Note that size limited to 500.");
  }
  catch(error){
    Logger.log("Warning! Unable to complete email search: " + error.message);
  } 

  return(threads);
}

function moveThreadsToTrash(threads, batchSize=100){

  // Work through the threads in batches (100 is the limit that batch operations can work on)
  for (j = 0; j < threads.length; j += batchSize) {

    // Select the current batch of threads
    var batch = threads.slice(j, j+batchSize);
    
    // Move the threads to the trash
    try{
      GmailApp.moveThreadsToTrash(batch);
      Logger.log("Moved " + batch.length + " threads to trash.");
    }
    catch(error){
      Logger.log("Warning! Unable to move threads to trash: " + error.message);
    }    
  }
}

function removeUnUselessEmails(){

  // Define the search string to identify emails 
  var searchString = "is:unread";

  // Get all threads matching search string
  var threads = getThreadsMatchingSearch(searchString);

  // Check whether thread search returned some results
  if (typeof threads !== 'undefined' && threads.length > 0) {
    
    // Mark as read and remove threads
    moveThreadsToTrash(threads);
  }  
}
```

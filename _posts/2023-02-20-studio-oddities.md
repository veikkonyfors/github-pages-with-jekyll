---
layout: default
title:  "Android Studio Oddities"
description: "Oddities in Android Studio which require further investigation until solved"
tag: Android & Kotlin
katex: true
---
# Android Studio Oddities

<br><br>


## R.java vs R.class vs fully qualified fi.viware.remeter_tabbedactivity.R
Had a problem with adding ActionBar threedot menu in my remeter_tabbedactivity app.

MainActivity makes use of ActivityMainBinding method instead of referring straight to the R attributes defined in res files.
This somehow prevents me from using R.drawable.ic_menu which I have in res/drawable/ic_menu.xml. It remains unresolved.  
I can use it in MenuActivity class, which is not using ActivityMainBinding, though.  

However, if I fully qualify it as fi.viware.remeter_tabbedactivity.R.drawable.ic_menu in MainActivity, then it gets resolved.
Hmm . . .

What I have learned so far is that in old times R.java was generated during the gradle build process to contain information in all res files. Then you were able to refer resources using R.*folder*/*resname*.xml type of name. 

My installation does NOT have any R.java available under the project. Learnt from chatGPT newer versions may generate R.class file on the fly. Without leaving any R.java behind, I assume. My installation did not have any R.class file either.  
  
Instead it has ./app/build/intermediates/compile_and_runtime_not_namespaced_r_class_jar/debug/R.jar, which seemingly contains all available resources in layout/activity_main.xml, which is binded using ActivityMainBinding. Unfortunately it doesn't seem to hold anything outside of layout/activity_main.xml.  
Or at least it doesn't have references to later on added resource files, even after clean+rebuild, project-gradle sync or even after invalidating caches.

If I fully qualify name as *fi.viware.remeter_tabbedactivity.R.drawable.ic_menu* in my binded MainActivity, then it gets resolved.  
Remember, in unbinded MenuActivity R was known without full qualification. Wonder from where that is coming. No R.java nor R.classwa available. Neither I am not able to edit the class file, no edit source code option available in context menu ... on down left corner. In MainActivity editing source in R context menu is available. Hmm . . . again.

**Solution**  
This all turned out to be due to an *import android.R* in my MainActivity. Which had been generated by AndroidStudio using *Tabbed Activity* template.  
AndroidStudio even displayed a warning *Don't include `android.R` here; use a fully qualified name for each usage instead*.  
Didn't notice it among the plethora of other warnings.  
MenuActivity, mentioned above, did not have import for android.R, thus worked without problem.  

Went through other apps I have generated, none of them had this problem. Seems to be out of templates I have used so far, *Tabbed Activity* is the only one incorporating import for android.R. Which shouldn't be there nowadays. Might be in old R.java days it was necessary to have it. Nowadays there is no R.java any more, resources are described in R.jar which is using app's package.  

More on [R story]( ../../../2023/02/27/R-module.html) 

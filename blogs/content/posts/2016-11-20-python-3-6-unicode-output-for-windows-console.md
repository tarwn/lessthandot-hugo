---
title: 'Python 3.5+: Unicode output for Windows Console'
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2016-11-20T16:10:02+00:00
ID: 4812
url: /index.php/desktopdev/python-3-6-unicode-output-for-windows-console/
views:
  - 2204
rp4wp_auto_linked:
  - 1
categories:
  - Desktop Developer
tags:
  - python
  - unicode

---
I&#8217;ve tripped over this on 2 machines now and end up at the same out of date StackOverflow post, so maybe this will help someone else.

<div style="border: #FFCCCC 2px solid; background-color: #FFDDDD; padding: 1em;">
  Python 3.5, not 3.6, I had that wrong initially 🙂
</div>

**Situation**
  
When you try to print a string with a Unicode character to the console on Windows in Python, you get:
  
`UnicodeEncodeError: 'charmap' codec can't encode characters in position 20-21: character maps to <undefined>`

Looking something like this:
  


<div id="attachment_4813" style="width: 810px" class="wp-caption alignleft">
  <a href="/wp-content/uploads/2016/11/python_console_unicode_error.png"><img src="/wp-content/uploads/2016/11/python_console_unicode_error.png" alt="Python UnicodeEncodeError" width="800" class="size-full wp-image-4813" srcset="/wp-content/uploads/2016/11/python_console_unicode_error.png 868w, /wp-content/uploads/2016/11/python_console_unicode_error-300x54.png 300w" sizes="(max-width: 868px) 100vw, 868px" /></a>
  
  <p class="wp-caption-text">
    Python UnicodeEncodeError
  </p>
</div>

**Fix**
  
Windows command-line supports unicode now, and Python 3.6+ ties into this support automatically (prior version requires win-unicode-console). You just need a slight bit of magic dust.

Add an environment variable named PYTHONIOENCODING to your environment settings, like so:
  


<div id="attachment_4814" style="width: 595px" class="wp-caption alignleft">
  <a href="/wp-content/uploads/2016/11/envvars.png"><img src="/wp-content/uploads/2016/11/envvars.png" alt="PYTHONIOENCODING Environment Variable" width="585" height="101" class="size-full wp-image-4814" srcset="/wp-content/uploads/2016/11/envvars.png 585w, /wp-content/uploads/2016/11/envvars-300x51.png 300w" sizes="(max-width: 585px) 100vw, 585px" /></a>
  
  <p class="wp-caption-text">
    PYTHONIOENCODING Environment Variable
  </p>
</div>

And now when you execute, you get console output instead of an error:
  


<div id="attachment_4815" style="width: 292px" class="wp-caption alignleft">
  <a href="/wp-content/uploads/2016/11/python_console_unicode_success.png"><img src="/wp-content/uploads/2016/11/python_console_unicode_success.png" alt="Python Console Output without Unicode Error" width="282" height="74" class="size-full wp-image-4815" /></a>
  
  <p class="wp-caption-text">
    Python Console Output without Unicode Error
  </p>
</div>
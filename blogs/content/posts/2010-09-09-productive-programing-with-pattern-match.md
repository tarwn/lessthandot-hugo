---
title: Productive Programing With Pattern Matching
author: chaospandion
type: post
date: 2010-09-10T00:36:58+00:00
url: /index.php/desktopdev/mstech/productive-programing-with-pattern-match/
views:
  - 3767
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft Technologies
tags:
  - .net
  - 'f#'

---
Personally I have been sold on F# for a long time but some people however will need a lot of convincing before they move out of their comfort zone. Hopefully after reading this you will be more inclined to try it out. 

Imagine you were given a directory full of files that did not have any file extensions and were told to figure out the file extension for every file. Picture how you might do this in VB or C#. Now take a look at this:

<pre>(*
Notice how we don't need to specify the type for filePaths?
The compiler will infer the type based off how we use the parameter.
When we call File.OpenRead the compiler will know that filePaths has to be a
string seq or IEnumerable&lt;string&gt; in C# speak.
*)
let getUnknownFileExtensions filePaths =
    filePaths
    |&gt; Seq.map(
        fun filePath -&gt;
            use fs = System.IO.File.OpenRead(filePath)
            let buffer = Array.zeroCreate 8
            let read = fs.Read(buffer, 0, 8)
            match buffer with
            (*The underscore tells the compiler that we dont care what the byte is at that index.*)
            | [| 0xFFuy; 0xD8uy; _; _; _; _; _; _; |] -&gt; 
                (filePath, ".jpg")
            | [| 0x25uy; 0x50uy; 0x44uy; 0x46uy; _; _; _; _; |] -&gt; 
                (filePath, ".pdf")
            | [| 0x50uy; 0x4Buy; 0x03uy; 0x04uy; _; _; _; _; |] -&gt; 
                (filePath, ".docx")
            | [| 0xD0uy; 0xCFuy; 0x11uy; 0xE0uy; 0xA1uy; 0xB1uy; 0x1Auy; 0xE1uy; |] -&gt; 
                (filePath, ".doc") 
            | _ -&gt; 
                (filePath, ".unk")
    )
    |&gt; Seq.iter(
        fun (filePath, extension) -&gt;
            System.IO.File.Move(filePath, filePath + extension)
    )</pre>

Now that is what I call productive programming!
---
layout: post
title: tic tac toe stylecop
---

As mentioned in an earlier post I wanted to try out FXCop. The first thing always to do is to read the Wikipedia article about the topic.

I learned that there is another option called [StyleCop](http://stylecop.codeplex.com/). It seems to be easier to adapt to a projects custom rules. This might come in handy, if something goes wrong. So I use StyleCop instead of FXCop.

The first step now is to enlist another target in the buildtool, so that StyleCop gets executed automatically with the same button push as all other tools. Fortunately it is already prepared and all that has to be done is to load the target after the CSharp targets were loaded. The sequence may be important because StyleCop will overwrite (or append itself to) a targets dependencies. It is easy to [find a tutorial on the web](https://www.google.de/#q=Import+Project+%22ProgramFiles%22+MSBuild+Microsoft+StyleCop+targets). Further down is a snippet of what I wrote into the project file.

On compiling it immediately showed 266 warnings but executed anyway. And since the only good rules are enforced rules, I tried to set the project to treat all warnings as errors. This did not achieve the result wanted, it seems that a different set of warnings is referenced by that entry. If you know all about this behaviour please comment below. Fortunately [StyleCop has an option](https://www.google.de/#q=%22StyleCopTreatErrorsAsWarnings%22) to treat warnings like errors. The option is listed in the example below. Beware of linebreaks, they can interfere with parsing the keyword "*false*" and lead to an error on triggering the buildtarget. Notice I did not use the placeholder for the program files directory. This somehow results in my Windows resolving that to a different directory. I am quite in the dark why that happens, it may be endemic to my machine.
<pre>&lt;Import Project="$(MSBuildToolsPath)\Microsoft.CSharp.targets" /&gt;
&lt;Import Project="C:\Program Files (x86)\MSBuild\StyleCop\v4.7\StyleCop.targets" /&gt;
&lt;PropertyGroup&gt;
&lt;StyleCopTreatErrorsAsWarnings&gt;false&lt;/StyleCopTreatErrorsAsWarnings&gt;
&lt;/PropertyGroup&gt;</pre>
StyleCop was quite picky about getting the comments right. I see some use for that in a company. For example having copyright headers in each file may be a good idea. Also interesting is enforcing comments on external methods and classes, so that the customer of an assembly knows how it is supposed to be used. In most of the cases I think commenting is bad style. Comments that mark sections usually can be formulated as a methodname containing that section. Comments explaining code usually only serve as a monument of a programmer giving up on the attempt to write a codeportion easy to read. Comments explaining a set of lines or a calculation are a [DRY](http://en.wikipedia.org/wiki/Don%27t_repeat_yourself) violation - information was written twice. Comments that exclude lines of code are overcautious, repositories save versions and filedeletion is not murder. Having said that - if there is no way of reminding future me how it all works - I will not hesitate to write a comment regardless of style.

I ended up removing all documentation rules. It was amazingly straight forward. Just find the [Settings.StyleCop](https://www.google.de/#q=%22Settings.StyleCop%22) file on your system and doubleclick it. If the installer did his job a specialized GUI will open and allow you to disable or enable any rules. On my system the file was located in <code>"C:\Program Files (x86)\StyleCop 4.7"</code>

The remaining 201 errors were minor things like naming conventions that are underestimated by a lot of programmers I know. If a whole company agrees on one set of style rules and enforces them, using a static codeanalysis tool makes it easier for everyone in that company to read the code.

I just feel better having a styleguard who watches my code, alerts me and stops compilations as long as I don't adress the style problems. Comment about your experience with enforced style rules.

The next steps will be to slowly add the initial features and illustrate how design patterns can help with the features, I initially wanted TicTacToe to have.

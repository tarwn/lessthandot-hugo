---
title: Learning Elixir and Phoenix – Environments and Editors
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2017-03-15T12:36:36+00:00
ID: 8594
url: /index.php/webdev/learning-elixir-and-phoenix-environments-and-editors/
views:
  - 3311
rp4wp_auto_linked:
  - 1
categories:
  - Web Developer
tags:
  - atom
  - elixir
  - phoenix
  - Vagrant
  - vi
  - vs code

---
Recently I started down the path to learn Elixir and the Phoenix framework. This is a language and framework I intend to use quite a bit, so my goal is to get from “barely able to read it” to “able to ship readable, idiomatic, testable apps”. 

Having learned a fairly large number of languages, libraries, and frameworks over the years, I know the best way I learn is by combining reading or videos with writing and debugging actual code (some intersection of chasing book smarts + street smarts). This also lets me combine my own experience and expectations with folks that are smarter about the given topic (and bring a different context), which sometimes results in learning things neither of us intended.

<div style="background-color: #FFFFCC; padding: 1em; margin: .5em; border: 1px solid #EEEEBB; border-left-width: 16px;">
  Note: I'm just getting started, so this is more a log of the things I've tried so far and is very much an amateurs take on things, not an experts. As I gain experience I'll likely have to revisit and correct myself.
</div>

I thought it might be handy to share that path, including problems and successes I run into along the way and will link in a distilled set of things that helped me along the way (assume for every link there were 5-20 more that were either less useful or I plain didn't understand yet).

## Step 1: Environments and Editors

Initially, I wanted to work predominately in a linux environment, so I set up a VirtualBox VM via Vagrant. Unfortunately, this ran into issues as I tried different editors (vi in an SSH session works great, an editor on the host does not). So far I've found little difference in running directly on Mac or Windows development machines, so I've switched to running directly on the machine in front of me.

One solution I have on the Mac side but not the Windows side (I haven't looked yet) is the ability to run specific versions of elixir for specific projects. The ability to support projects running on individually specified versions means I don't risk break all of my projects when I start a new one on an updated version. I also can ensure that pulling a repo down to a second machine will allow me to run in the same version, instead of losing an afternoon to an unepected bug hunt/version upgrade.

### Environments: Windows

Web Installer (global version): <http://elixir-lang.org/install.html#windows>

I didn't run into any notable issues but this is a global version, so looking into something for windows like asdf below is high on the list the next time I work on the Windows system.

### Environments: Mac

I've tried this two ways on the Mac, one global version via Homebrew and one project-specific version via [asdf][1]. Right now I'm preferring the latter, as it gives me that project-specific version capability I mentioned above.

Homebrew Approach (global install): <http://elixir-lang.org/install.html#mac-os-x>

“asdf” Approach (project-specific versioning):

  * [Install asdf][2]
  * [Install asdf-erlang plugin][3]
  * [Install asdf-elixir pluing][4]
  * [Install asdf-nodejs plugin][5]

There is a good walk-through here: [Elixir & Erlang Setup with asdf Version Manager][6]

I include the nodejs plugin in my list because the Phoenix framework relies on nodejs and npm as part of it's build tools ([brunch.io][7]), and we might as well version everything instead of going half and half.

The biggest issue I ran into was the nodejs plugin, as it relies on a bunch of gpg keys being registered on your machine and my machine was absolutely convinced that it didn't want to do gpg and documentation on the error was not very helpful.

**Error:** 

```text
Unable to execute program '/usr/local/Cellar/gnupg/1.4.21/libexec/gnupg/gpgkeys_curl': No such file or directory
Gpg: no handler for keyserver scheme 'hkp'
```
**Resolution:**
  
After flailing a bit with different gpg installation options, internet posts indicating I needed to build from scratch, etc, the answer ended up being to brew install curl and then uninstall and reinstall gpg via brew as well. This isn't 100% for sure, though, as there may have been remnants of other attempts (such as brew installing gpg2, manually installing gpg and brew force linking it and then uninstalling it, etc).

### Environments: Vagrant VM running Trusty64

In this case, a global version is acceptable because it would be partitioned to the vagrant VM image.

  * I used this as a starting point: <https://github.com/lau/vagrant_elixir> 
  * Switched it to trusty64 (really only because I already had that image downloaded)
  * Added a machine name because the default is too long
  * vagrant up, and everything is working

I set up my project in the shared /vagrant folder, occasionally using vi directly (vagrant ssh) and occasionally trying to use IDE tools on my desktop.

## Editors

Here are the editors I've tried so far:

### vi

I started with vi for both the vagrant environment and mac. It's my go to editor on a *nix system, even if I'm a bit rusty. It was workable, but honestly I've been spoiled by visual editors for too long and decided to shelve it and come back later.

### Atom

Find it here: [Atom Editor][8]

I tried Atom for all 3 environments, in the vagrant case running it on the host and editing files in the shared folder to the vagrant system.

Packages:

  * [language-elixir][9] – Syntax highlighting and snippets
  * [linter-elixirc][10] – Linting for elixir
  * [minimap][11] – not elixir-specific, it's a birds eye scrollbar (something I started liking years ago with [metalscroll][12])
  * [autocomplete-elixir][13] – autocomplete dropdowns, type hints, etc

I had some teething troubles with at least one of the elixir ones, it would get very unhappy when it couldn't find elixir (which occurred in folders that asdf had not been installed in yet). On a positive note, having auto-indention, autocompletion of things like defmodule do/end, syntax coloring, and so on are a basic set of expectations for me when using any IDE. I also get instant re-compilation of code to show errors and warnings, which is also handy (no regular switches to command line to run the build myself and wait for the result, just instant feedback). One thing that stood out to me is that Atom tends to hide everything by default and the occasional plugin error liked to beat me in the face with its error messages.

### VS Code

Find it here: [VS Code Editor][14]

I only tried VS Code on the windows environment so far (it has been my go -to python editor for a little while). Like Atom, it needed a version of elixir installed in order for extensions to work properly.

Extension: [vscode-elixir][15] – syntax coloring, snippets, intellisense

I had a little more familiarity with VS Code already, but I haven't used it in a couple months. Like Atom, VS Code relies heavily on you learning lots of keyboard shortcuts, search menus, etc. Something I had not done much with either. I have found VS Code to be a little more discoverable than Atom, partially due to a number of the most important (to me) features sitting on a sidebar by default, where Atom starts you off with more-or-less an empty screen (and a Welcome screen I can't seem to make stop showing up). Also, they've really handled things like performing simple git commits quickly very easy, I've long ignored or disliked just about every IDE version control plugin I've tried, this one does that one simple, small thing very well.

I've been using Atom far more, so I haven't tried step-through debugging and have less hands one experience with the capabilities it shares with Atom. I've obviously used a lot of versions of Visual Studio in the past (11 I think?) and don't want to default to something familiar if I can push my boundaries a bit and maybe find something in Atom I didn't know I was missing.

# Next Up: Learning to Code

So, I have a set of environments and some editors, next up is figuring out how the heck to write in the language, what the idioms are, and then throw a completely new web framework (and template language, and ORM, and nodejs tools, and...) on top of the mix. See you there!

 [1]: https://github.com/asdf-vm/asdf
 [2]: https://github.com/asdf-vm/asdf#setup
 [3]: https://github.com/asdf-vm/asdf-erlang
 [4]: https://github.com/asdf-vm/asdf-elixir
 [5]: https://github.com/asdf-vm/asdf-nodejs
 [6]: https://www.icicletech.com/blog/elixir-and-erlang-setup-with-asdf-version-manager "Walk-throguh of Elixir and Erlang setup on asdf"
 [7]: http://brunch.io/
 [8]: https://atom.io/
 [9]: https://atom.io/packages/language-elixir
 [10]: https://atom.io/packages/linter-elixirc
 [11]: https://atom.io/packages/minimap
 [12]: /index.php/desktopdev/mstech/visual-studio-metalscroll-add-on/
 [13]: https://atom.io/packages/autocomplete-elixir
 [14]: https://code.visualstudio.com/
 [15]: https://marketplace.visualstudio.com/items?itemName=mjmcloug.vscode-elixir
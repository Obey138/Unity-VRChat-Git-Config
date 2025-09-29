Based on [NYUGameCenter/Unity-Git-Config](https://github.com/NYUGameCenter/Unity-Git-Config)  
  
This fork contains changes made to accommodate Unity projects made for VRChat user generated content, that use VRChat Creator Companion and VRChat SDK  
  
1. Create a project in Creator Companion or use an existing one  
2. Paste these files into the project folder  
3. Copy files from `/githooks` to `.git/hooks`  
4. Edit `.gitconfig` - provide path to UnityYAMLMerge (this depends on where Unity is installed on your machine)  
5. In Unity, open Project Settings  
6. Force visible .meta files    `Version Control / Mode: “Visible Meta Files”`  
7. Force text serialization    `Asset Serialization / Mode: “Force Text”`  
8. Save  
9. `git init`  
10. `git lfs install`  
    (LFS should be bundled with Git for Windows official installer. If this doesn't work, [install Git LFS first using official installer](https://git-lfs.com/))  
12. Make first commit  
13. Enjoy!  
  
> [!IMPORTANT]  
> When pushing a newly git initiated project to remote for the first time, make sure remote is empty (no initial commit). Otherwise conflicts might occur  
  
I also recommend getting [spoiledcat/git-for-unity](https://github.com/spoiledcat/git-for-unity) for a git GUI inside Unity  
  
<br>  
<br>  
<br>  
  
> [!WARNING]  
> Readme rework TODO  
> The rest of this file is unchanged from original repo  
  
## Configure Unity for Git

1. Create a new unity project.

2. Open the editor settings:

   `Edit > Project Settings > Editor`

3. Force visible .meta files (this will ensure script execution order & object references are maintained)

   `Version Control / Mode: “Visible Meta Files”`

4. Force text serialization (this will ensure you can merge & properly diff your assets)

   `Asset Serialization / Mode: “Force Text”`

5. Save changes

   `File > Save Project`

## Create and Configure Your Repo 

1. Create a new github repo with the same name as your Unity project. Don't select the default Unity .gitignore, we'll be importing our own later.

2. Clone the repo to the Unity project folder you created in "Configure Unity for Git".

   >If you can't see the `/.git/` folder, make it visible by following [these steps for Windows](https://kb.wisc.edu/page.php?id=27479) or [these steps for Mac](https://ianlunn.co.uk/articles/quickly-showhide-hidden-files-mac-os-x-mavericks/).

3. Download the .gitconfig, .gitignore,  and .gitattributes file from this into the root of the local repo you just cloned, ie into the folder `<your_repo>/`.

4. Edit .gitconfig with a text editor, replacing `<path to UnityYAMLMerge>` with the location of your Unity install's merge tool (note that these locations can vary if you picked a different install folder during unity install. Also note that you will need to edit this file again if you upgrade Unity & choose a different install location.)
    >On Windows it's usually: `C:\\Program Files\\Unity\\Editor\\Data\\Tools\\UnityYAMLMerge.exe` or `C:\\Program Files (x86)\\Unity\\Editor\\Data\\Tools\\UnityYAMLMerge.exe` (double slashes are necessary as escape characters).  

    >On Mac it's usually: `/Applications/Unity/Unity.app/Contents/Tools/UnityYAMLMerge`.   

   This merge tool will try to merge or resolve conflicts within .prefab, .scene, and other unity asset files. If it can't do it automatically, your default merge tool will open & you can manually select which changes to include.  
   **Always open any merged unity assets & confirm the merge worked before pushing the merged assets.** For more info, check [this git hub post](https://github.com/anacat/unity-mergetool) or [this blog post](http://www.jameskeats.com/blogs/post/Unitys-SmartMerge-Meets-SourceTree/).

5. Copy the contents of your Unity project into the new folder.

6. Commit these changes to your new repo & push. Your new project should look like this on Github:


## Install GitLFS 

1. Download & install git-lfs from https://git-lfs.github.com/. If you've already installed git-lfs, proceed to step 2.

2. Open a command prompt, terminal, or gitbash window. 

3. Navigate to the folder containing your git repository & execute: `git lfs install`

4. That's all you need to do, as tracking the appropriate files in lfs is taken care of by the .gitattributes file. If you're already familiar with git, you might consider reading [this intro to git-lfs](https://github.com/git-lfs/git-lfs/wiki/Tutorial), as working with it varies from vanilla git quite a bit.

5. Download the pre-commit & post-merge scripts. Enable them in your repo by moving them into the folder `<your_repo>/.git/hooks/`.  These will ensure that meta files stay in sync. It will also alert you if you attempt to commit a >100mb file, which github will reject. It will reject the commit, allowing you to revise it to remove or reduce the size of the offending file(s). **These scripts have to be installed individually on each computer you clone the repo to. Please ensure your teammates have installed these as well.**

6. On OSX, you must make these hooks executable by chmod +x. https://support.apple.com/guide/terminal/make-a-file-executable-apdd100908f-06b3-4e63-8a87-32e71241bab4/mac

## Invite Teammates

1. Make sure they've all installed git lfs!

2. Add them to your repo.

3. Help them clone the repo, copy the `pre-commit` & `post-merge` scripts into `/.git/hooks/`, and setup the .gitconfig file for their system (steps 3-5 of Create & Configure Your Repo).

# Usage and Errors

Now that you have these hooks installed, they'll automatically stop you if you try to commit files that might mess up your project. There are a few ways this can happen.

## Attempt to Commit File >100MB
If you try to commit a file that's bigger than 100MB, you'll see an error like this one:

>Commit failed with error
>			0 files committed, 1 file failed to commit: testing  
 >			`Assets/big.pdf` is over 100MB.  
 >			Can't commit, fix errors first.  

Resolve this error by reducing the size of the file. For audio or visual assets, try splitting them into smaller parts or compressing them. For unity .scene files, try to reduce the scene size by dragging elements out of the scene and into prefabs.

## Failure to Commit Metafile
If you try to commit an asset without a corresponding metafile, you'll see an error like this one:

>Commit failed with error
>			0 files committed, 1 file failed to commit: testing . 
 >			Error: Missing meta file.  
 >			Asset `Assets/LensFlare.flare` is added, but `Assets/LensFlare.flare.meta` is not in the git index.  
 >			Please add `Assets/LensFlare.flare.meta` to git as well.

Resolve this error by adding the corresponding .meta file to your commit.

## Failure to Add Corresponding Metafile to Gitignore

If you add a file or folder to the .gitignore, but don't add the corresponding .meta to your .gitignore, you'll see an error like this one:

>Commit failed with error  
 >			0 files committed, 3 files failed to commit: testing  
 >			`LensFlare.flare`  
 >			`LensFlare.flare` found in .gitignore but not the corresponding meta file! Please add `LensFlare.flare.meta` to .gitignore . 

Resolve this error by editing your .gitignore by adding the .meta file to it. 

Always exercise caution when adding to your .gitignore: it's not a tool for resolving bad commits (see the [troubleshooting](#troubleshooting) section for help resolving issues with commits). Never add .meta files to the .gitignore without also adding the file associated with that meta file to the .gitignore. Adding files to your .gitignore will result in them not being backed up & nor shared with your collaborators. 

# Troubleshooting

Did everything above & still managed to run into a problem? Here are some of the best resources for learning how to restore your repo. 

 * [On undoing, fixing, or removing commits in git](https://sethrobertson.github.io/GitFixUm/fixup.html) - A choose-your-own-adventure style troubleshooter
 * [Oh shit, git!](https://ohshitgit.com/) - Lots of common use cases, expressed simply & crassly
 * [10 Common Git Problems](https://www.codementor.io/citizen428/git-tutorial-10-common-git-problems-and-how-to-fix-them-aajv0katd) - Similar to the above, less crass
 * [git-lfs Troubleshooting](https://github.com/git-lfs/git-lfs/wiki/Troubleshooting) - Common issues folks run into while using git-lfs

# Additional Information

Want more info on how we built these config files & hooks? Here's some of the docs & posts we read when building this: 
 
  * http://www.gamasutra.com/blogs/TimPettersen/20161206/286981/The_complete_guide_to_Unity__Git.php 
  * http://www.edwardthomson.com/blog/git_with_unity.html
  * https://riptutorial.com/unity3d/example/7179/setting-up-a-git-repository-for-unity
  * https://thoughtbot.com/blog/how-to-git-with-unity
  * http://teaclipper.co.uk/2016/11/11/the-perfect-unity-git-repo/
  * http://www.jameskeats.com/blogs/post/Unitys-SmartMerge-Meets-SourceTree/
  * https://nagachiang.github.io/tutorial-setup-smart-merge-for-unity-assets-with-git/
  * https://www.forrestthewoods.com/blog/managing_meta_files_in_unity/

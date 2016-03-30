git-blame
=========

* Show what revision and author last modified each line of a file

This is one of the more useful commands in git arsenal. It gives you information on who, when and in what revision did last modification to that specific line. If follows whole-file renames and can even track lines moved/copied from other files (you know, for those situations when you have one file copied into 4 with just minor modifications).

> Since it annotates lines in file, it tells you nothing about lines that were deleted or completely replaced. Read up on `git-log`'s `-S` option for that.

Preparing example environment
-----------------------------

First we will prepare environment to show usage of `git-blame` on. We need some data to show it on, so I've modified my `MyWindowsApp/windows.txt` to look like this:

	$ cat MyWindowsApp/windows.txt 
	// Added during git workshop

	// windows
	int main ( int argc , char * * argv ) {
		return 0;
	}

in one commit and after that made two other changes in separate commit:

	$ cat MyWindowsApp/dll.txt 
	// Added during git workshop

	// windows
	int main ( int argc , char * * argv ) {
		return 0;
	}

	// dll version

and in one last commit to some additional modification to `MyWindowsApp/windows.txt` file, I did

	$ echo "// end-of-file" >> MyWindowsApp/windows.txt
	$ git add -u
	$ git commit -m "added comment marking end of file"
	[master eb6e5f6] added comment marking end of file
	 1 file changed, 1 insertion(+)

Who's to blame?
---------------

Now we can get to actually determining the blame.

	$ git blame MyWindowsApp/windows.txt
	dc2331b3 (Wolf 2016-03-31 01:34:17 +0200 1) // Added during git workshop
	dc2331b3 (Wolf 2016-03-31 01:34:17 +0200 2) 
	dc2331b3 (Wolf 2016-03-31 01:34:17 +0200 3) // windows
	dc2331b3 (Wolf 2016-03-31 01:34:17 +0200 4) int main ( int argc , char * * argv ) {
	dc2331b3 (Wolf 2016-03-31 01:34:17 +0200 5)     return 0;
	dc2331b3 (Wolf 2016-03-31 01:34:17 +0200 6) }
	eb6e5f64 (Wolf 2016-03-31 01:44:43 +0200 7) // end-of-file

What we can see here?

1. in what commit the change was made
2. who made it
3. when
4. line number
5. line content

This seems useful, right? But wait, we can do more! We all know that it shouldn't be done but it's still is. Copy&paste programming is sad reality and can really complicate finding out who originaly wrote what line and where. But git to the rescue! First let's run blame on `MyWindowsApp/dll.txt` to see the result:

	$ git blame MyWindowsApp/dll.txt
	e2385cf8 (Wolf 2016-03-31 01:34:40 +0200 1) // Added during git workshop
	e2385cf8 (Wolf 2016-03-31 01:34:40 +0200 2) 
	e2385cf8 (Wolf 2016-03-31 01:34:40 +0200 3) // windows
	e2385cf8 (Wolf 2016-03-31 01:34:40 +0200 4) int main ( int argc , char * * argv ) {
	e2385cf8 (Wolf 2016-03-31 01:34:40 +0200 5)     return 0;
	e2385cf8 (Wolf 2016-03-31 01:34:40 +0200 6) }
	e2385cf8 (Wolf 2016-03-31 01:34:40 +0200 7) 
	e2385cf8 (Wolf 2016-03-31 01:34:40 +0200 8) // dll version

It looks like I wrote those lines in commit e2385cf8, but we know they originated from the other files. So let's find true origin of those lines.

	$ git blame -C -C -C MyWindowsApp/dll.txt
	dc2331b3 MyWindowsApp/windows.txt (Wolf 2016-03-31 01:34:17 +0200 1) // Added during git workshop
	dc2331b3 MyWindowsApp/windows.txt (Wolf 2016-03-31 01:34:17 +0200 2) 
	dc2331b3 MyWindowsApp/windows.txt (Wolf 2016-03-31 01:34:17 +0200 3) // windows
	dc2331b3 MyWindowsApp/windows.txt (Wolf 2016-03-31 01:34:17 +0200 4) int main ( int argc , char * * argv ) {
	dc2331b3 MyWindowsApp/windows.txt (Wolf 2016-03-31 01:34:17 +0200 5)    return 0;
	dc2331b3 MyWindowsApp/windows.txt (Wolf 2016-03-31 01:34:17 +0200 6) }
	e2385cf8 MyWindowsApp/dll.txt     (Wolf 2016-03-31 01:34:40 +0200 7) 
	e2385cf8 MyWindowsApp/dll.txt     (Wolf 2016-03-31 01:34:40 +0200 8) // dll version

Ha! See the difference? Now we know that first 6 lines are copy&paste from `MyWindowsApp/windows.txt`.

> Why are there multiple `-C` options? Git allows 0 - 3 of them, they determines how hard git looks for matching lines. See `man git-blame` for more details.

> You should know that `git-blame -C` by default requires at least 40 characters to match some other file to consider it copy&paste. This can be fine-tuned by added number after **last** `-C` (`git blame -C -C20`).

> One last good-to-know option is `-w` which forces git to ignore white space changes when looking for matching lines.

git-gui blame
-------------

This text is on command line git usage, so this will not be described, but seriously, take some time to play with `git gui blame`. Lot of really cool features, my go-to tool for code archeology.

More informations
-----------------

I recommend checking [git-blame documentation](https://git-scm.com/docs/git-blame) for more details, there are some other useful options.

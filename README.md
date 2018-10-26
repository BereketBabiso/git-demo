=========git commands=============
1. Install the git installer
2. set user name just to explicitly inform git that anything add to git is users
   command: git config --global user.name "Bereket Babiso"
3. set email address as well for same reason as part(2)
   comman: git config --global user.email "bereketb.yetera@gmail.com"

4. git init MyProject1 // creates a git master project in the git directory
5. cd MyProject1  // changes diretory to MyProject1
6. ls -a //to show whether a .git file exists in the directory
7. touch filename.filetype(for ex README.md) // to create a new file
8. vi README.md // this opens the vim text editor to write of README.md , once done writing
    		// click ESC and type :wq to close the vim editor
9. git add  README.md // adds the file to the working branch
10. git commit -m "commit message" // commits the change to the working branch
11. git branch // shows the available branches
12. git status // shows the status on the working branch
13. ls  // list all the files in the branch
14. git checkout -b develop // creates a new develop branch from the master branch
15. git checkout -b feature-1.0 // creates a new feature from the current working develop branch
16. 
17. git push -u origin master  // push the master branch
18. git push -u origin feature-1.0 // push the feature branch
19. git push -u origin develop // push the develop branch
20. git add -p , then select "y" ======> this is to stage hunk
21. git push -u origin feature/CapUserIdManager

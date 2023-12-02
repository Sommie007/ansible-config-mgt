# GIT AND GITHUB PROJECT IMPLEMENTATION
 ## **This project shows how to implement GIT AND GITHUB**

## ***INITIALIZING GIT REPOSITORY***

- Firstly [install Git](https://git-scm.com/downloads)

- Open a terminal on your computer e.g ***git Bash***

- Creat a working directory on terminal using this command **`mkdir DevOps`**

- Change to the directory or folder using this command **`cd DevOps`**

- while in the directory to initialize the directory use this command **`git init`**

    ![Alt text](DevOps-Git/git%20init.png)

## ***MAKING YOUR COMMIT***

- Create an index.txt file or use any name you choose to create a file in your working directory using this command **`touch index.txt`**

- Input into the file any sentence of your choice, then save your 
  changes

- Add your changes to the staging area using this command **`git add . `**

- Commit your changes using this command **`git commit -m "initial commit `**
![Alt text](DevOps-Git/git%20commit.png)

## ***WORKING WITH BRANCHES***

- ## *Create your first git branch*

- Create a new branch using this command **`git checkout -b my-new-branch`** use can use any name of your choice other than "my-new-branch"

    ![Alt text](DevOps-Git/git%20checkout.png)

- ## *List your git branch*

- To list your git branch, use the command **`git branch`**
![Alt text](DevOps-Git/git%20branch.png)

- ## *Change into an old branch*

- Switching to another branch, use the command **`git branch <branch-name>`**
![Alt text](DevOps-Git/git%20checkout%20to%20another%20branch.png)

- ## *Merge one branch with another branch*

- Merging one branch to another branch, use the command **`git merge <branch-name> `**

    ![Alt text](DevOps-Git/git%20checkout%20to%20another%20branch.png)

- ## *Deleting a git branch*

- To Delete a git branch, use this command **`git branch -d <branch-name> `** 

## **COLLABORATION AND REMOTE REPOSITORIES**
- Create a git hub account for the [Github site](https://github.com/)

- ## *Create a repository*

  ![Alt text](<DevOps-Git/create a repo.png>)

  You can create a respository on github and make it public for others to have access to it or private for just restricted access.

- ## *Pushing a local github repository to a remote github repository*

- Add a remote repository to a local repository, use the command
  **` git remote add origin <link to your github repo> `**
![Alt text](DevOps-Git/git%20remote%20add.png)

- To push the origin or remote, use this command **`git push orgin <branch-name> `**
![Alt text](DevOps-Git/git%20push%20origin.png)

- ## *Cloning a Remote git repository*

- To clone to remote repository, use this command **` git clone <link to remote repository> `**
![Alt text](DevOps-Git/git%20clone.png)


- ## *Bracnch management and tagging*

- ## *introduction to markdown syntax*

- Headings

  To create a heading, add number signs (#) in front of a word or phrase. The number of number signs you use should correspond to the heading level.

  # Heading level 1
  ## Heading level 2
  ### Heading level 3

- Italic

  To italicize text, add one asterisk or underscore before and after a word or phrase. To italicize the middle of a word for emphasis, add one asterisk without spaces around the letters.

  Italicized text is the <em>cat's meow</em>

  Italicized text is the <em>cat's meow</em>

- Lists

  You can organize items into ordered and unordered lists.

- Ordered Lists

  To create an ordered list, add line items with numbers followed by periods. The numbers don’t have to be in numerical order, but the list should start with the number one.

  <ol>
  <li>First item</li>
  <li>Second item</li>
  <li>Third item</li>
  <li>Fourth item</li>
  </ol> 

- Unordered Lists

  <ul>
  <li>First item</li>
  <li>Second item</li>
  <li>Third item
    <ul>
      <li>Indented item</li>
      <li>Indented item</li>
    </ul>
  </li>
  <li>Fourth item</li>
</ul> 

  Links

  To create a link, enclose the link text in brackets (e.g., [Duck Duck Go]) and then follow it immediately with the URL in parentheses e.g My favorite search engine is [Duck Duck Go](https://duckduckgo.com)

  This link [mark documentation](https://learn.microsoft.com/en-us/contribute/content/markdown-reference) would assist with more markdown syntax.

  ### ***Thank you !!!***
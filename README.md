# README.md

- source for hugo based website. 
- go to the websource directory
- make updates as required see [https://georgecushen.com/create-your-website-with-hugo/]
- commit the changes to websource on github


Now check the actual website readme. It talks about how you do the public folder...
In a nutshell:

- clone the website repository (not the websource repository) to the public folder
- git clone https://github.com/t-student/t-student.github.io.git public
- cd to the websource directory and run hugo (literally, just type hugo and return).
- this updates the public directory (which is the source for the deployed website)
- cd public and push the deployed website changes
- cd back out of public and blow it away if you want...

Clear?

And, to run the hugo server on your local machine, cd to the websource directory and type `hugo server -D`.

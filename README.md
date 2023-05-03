Download Link: https://assignmentchef.com/product/solved-cs142-project6-appserver-and-database
<br>
In this project you will start up a database system and convert your Photo Sharing App you built in Project #5 to fetch the views’ models from it. We provide you a new webServer.js supporting the same interface as Project #5’s web server but it also establishes a connection to a database. This allows you to make your app into a legitimate

full stack application.

<h1> Setup</h1>

You should have MongoDB and Node.js installed on your system. If not, follow the <a href="https://web.stanford.edu/class/cs142/install.html">installation instructions (install.html) </a>now.

Project #6 setup is di erent from the previous projects. You start by making a copy of your project5 directory les into a directory named project6 . Into the project6 directory extract the contents of <a href="https://web.stanford.edu/class/cs142/downloads/project6react.zip">this zip le (downloads/project6react.zip)</a>. This zip le will overwrite the les package.json , webServer.js , .eslintrc.json , and index.html and add several new les and directories. In the unlikely event you had made necessary changes in any of these les in your project5 directory you will need

to reapply the changes after doing the unzip.

Once you have installed MongoDB and created the directory for the database as described in the <a href="https://web.stanford.edu/class/cs142/install.html">installation instructions (install.html)</a>, you can start MongoDB by running the command:

Since this command doesn’t return until the database is shutdown you will want to either run it in a separate window or as a background process (e.g. mongod &amp; on Linux/MacOS).

Once the MongoDB server is started you can load the photo app data set by running the command:

This program loads the fake model data from previous projects (i.e. modelData/photoApp.js ) into the database. Since our app currently doesn’t have any support for adding or updating things you should only need to run loadDatabase.js once. The program erases whatever is in the database before loading the data set so it is safe to run multiple times.

We use the <a href="http://mongoosejs.com/">MongooseJS (http://mongoosejs.com) </a>Object De nition Language (ODL) to de ne a <a href="http://mongoosejs.com/docs/guide.html">schema (http://mongoosejs.com/docs/guide.html)</a> to store the photo app data in MongoDB. The schema de nition les are in the directory schema :

schema/user.js – De nes the User collection containing the objects describing each user.

schema/photo.js – De nes the Photos collection containing the objects describing each photo. It also de nes the objects we use to store the comments made on the

photo. schema/schemaInfo.js – De nes the SchemaInfo collection containing the object describing the schema version.

These les are loaded both into the loadDatabase.js program where they are used to create the database and the webServer.js where they are used to access the database. Note: The object schema stored in the database is similar to but necessarily di erent from the cs142models JavaScript objects used in the previous assignment. Familiarize yourself with these schema de nitions.

<h1> Start the Node.js web server</h1>

Once you have the database up and running you will need to start the web server. This can be done with the same command as the previous assignments (e.g. node webServer.js ). Start your web server with the command from your project6 directory:

If you use the above command, remember to <strong>restart the web server after each change you make to the server code.</strong> You can also use nodemon , which will watch for any changes to the server code and automatically restart the web server:

After updating your Photo Share App with the new les from Project #6 and starting the database and web server make sure the app is still working before continuing on to the assignment.

<h1> Problem 1: Convert the web server to use the database</h1>

The webServer.js we give you in this project is like the Project #5 webServer.js in that the app’s model fetching routes use the magic cs142models rather than a database. Your job is to convert all the routes to use the MongoDB database. There should be no accesses to cs142models in your code and your app should work without the line:

in webServer.js . Note that any console.log statements in webServer.js will print to the terminal rather than the browser.

<h2>Web Server API</h2>

As in Project #5 the web server will return JSON encoded model data in response to HTTP GET requests to speci c URLs. We provide the following speci cation of what URLs need to be support and what they should return. Your web server should support the following model fetching API:

<strong>/test</strong> – Return the schema info ( /test/info ) and object counts ( /test/counts ) of the database. This interface is for testing and as an example for you we provide an implementation that fetches the information from the database. You will not have to change this one.

<strong>/user/list</strong> – Return the list of users model appropriate for the navigation sidebar list. Since we anticipate large numbers of users, this API should only return an array of user properties needed by the navigation side bar ( _id, first_name, last_name ). It replaces the cs142models.userListModel() call in the provided code.

<strong>/user/:id</strong> – Return the detail information of the user with ID of id. This should return the information we have on the user for the detail view ( _id, first_name, last_name, location, description, occupation ) and replaces the cs142models.userModel() call. If some thing other than the id of a User is provided the response should be an HTTP status of 400 and an informative message.

<strong> /photosOfUser/:id</strong> – Return the photos of the user with _id of id. This call generates all the model data needed for the photos view including all the photos of the user as well as the comments on the photos. The photos properties should be ( _id, user_id, comments, file_name, date_time ) and the comments array elements should have ( comment, date_time, _id, user ) and only the minimum user object information ( _id, first_name, last_name ). This replaces the

cs142models.photoOfUserModel() call. If some thing other than the id of a User is provided the response should be an HTTP status of 400 and an informative

<a href="https://github.com/caolan/async/blob/v1.5.2/README.md">message. Note this API will take some assembling from multiple di erent objects in the database. The assignment’s </a><a href="https://github.com/caolan/async/blob/v1.5.2/README.md">package.json</a><a href="https://github.com/caolan/async/blob/v1.5.2/README.md"> le fetches the async (https://github.com/caolan/async/blob/v1.5.2/README.md)</a> <a href="https://github.com/caolan/async/blob/v1.5.2/README.md">module to make the assembling the multiple photos easier.</a>

To help you make sure your web server conforms to the proper API we provide a test suite in the sub-directory test . <strong>Please make sure that all of the tests in the suite pass before submitting.</strong> See the Testing section below for details.

Your GET requests do not return exactly the same thing that the cs142models functions return but they do need to return the information needed by your app so that the model data of each view can be displayed with a single FetchModel call. You will need to do subsetting and/or augmentation of the objects coming from the database to build your response to meet the needs of the UI. For this assignment you are not allow to alter the database schema in anyway.

<a href="http://mongoosejs.com/docs/models.html">Implementing these Express request handlers requires interacting with two di erent “model” data objects. The Mongoose system returns models</a>

<a href="http://mongoosejs.com/docs/models.html">(http://mongoosejs.com/docs/models.html) from the objects stored in MongoDB while the request itself should return the data models needed by</a> the Photo App views. Unfortunately since the Mongoose models are set by the database schema and front end models are set by the needs of the UI views they don’t align perfectly. Handling these requests will require processing to assemble the model needed by the front end from the Mongoose models returned from the database.

Care needs to be taken when doing this processing since the models returned by Mongoose are JavaScript objects but have special processing done on them so that any modi cations that do not match the declared schema are tossed. This means that simply updating a Mongoose model to have the properties expected by the front end doesn’t work as expected. One way to work around this is to create a copy of the Mongoose model object. A simple way of doing the copy is to translate the model into JSON and back to an JavaScript objects. The following code fragment does this object cloning:

by taking modelObject converting into a JSON string and then converting it back to a JavaScript object, this time without the methods and special handling done on Mongoose models.

<h1> Problem 2: Convert your app to use axios</h1>

In preparation for the next assignment where we will use more of the REST API convert your photo app to use axios rather than your own FetchModel routine to fetch the models from the web server. <a href="https://github.com/axios/axios">Axios (https://github.com/axios/axios) </a>is one of the many npm packages available for fetching data. It is faster and has more functionality than our FetchModel function, and it is what we will use to make our requests going forward. Your photo app should work without the FetchModel function de nition after nishing this problem. The functionality of your app should be exactly the same before and after removing the FetchModel function de nition — make sure you do not break anything in the process of making this switch.

<a href="https://github.com/axios/axios">In particular, any time you call </a><a href="https://github.com/axios/axios">FetchModel</a><a href="https://github.com/axios/axios"> in </a><a href="https://github.com/axios/axios">photoShare.jsx</a><a href="https://github.com/axios/axios"> or your other components, you should replace it with a call to </a><a href="https://github.com/axios/axios">axios.get</a> <a href="https://github.com/axios/axios">. </a><a href="https://github.com/axios/axios">See the </a><a href="https://github.com/axios/axios">axios</a> <a href="https://github.com/axios/axios">documentation (https://github.com/axios/axios) for more details.</a>

Notice that axios.get returns a Promise, much like FetchModel . We can attach success and failure handlers using .then and .catch respectively. You shouldn’t have to change the logic of your handlers from FetchModel , very much, or at all, when attaching them to axios.get .

<h1> Testing</h1>

<a href="https://mochajs.org/">Testing a full web application is challenging. In the directory </a><a href="https://mochajs.org/">test</a><a href="https://mochajs.org/"> we provide a test of just the backend portion of your application. The test uses Mocha</a>

<a href="https://mochajs.org/">(https://mochajs.org), a popular framework for writing Node.js tests. To setup the test environment from inside the </a><a href="https://mochajs.org/">test</a><a href="https://mochajs.org/"> subdirectory do an </a><a href="https://mochajs.org/">npm insta</a>ll to fetch Mocha and all the related dependencies. Once you have done this you can run the test running the command: npm test .

The npm test command runs the le test/serverApiTest.js which is a program written in the Mocha language (e.g. describe() and it() ) testing the three Photo App backend URLs ( /user/list, /user/ID, /photosOfUser/ID ). <strong>In order to be reasonably sure that the functionality of the backend routes conforms to spec, please check that all our provided tests pass before submitting. A portion of your project grade will be based on how many of these tests you pass.</strong>

<h1> Extra Credit</h1>

Your Photo App’s marketing department has come up with a “small” tweak to the app to make it more social network friendly. The change is:

<a href="https://www.google.com/search?q=count+bubbles+UI&amp;rlz=1C5CHFA_enUS503US503&amp;espv=2&amp;biw=1440&amp;bih=782&amp;source=lnms&amp;tbm=isch&amp;sa=X&amp;ved=0ahUKEwjS7v2Ig4LLAhVM8GMKHTAlC8IQ_AUIBigB">The side navigation bar containing the list of users shall include two count bubbles (https://www.google.com/search?</a>

<a href="https://www.google.com/search?q=count+bubbles+UI&amp;rlz=1C5CHFA_enUS503US503&amp;espv=2&amp;biw=1440&amp;bih=782&amp;source=lnms&amp;tbm=isch&amp;sa=X&amp;ved=0ahUKEwjS7v2Ig4LLAhVM8GMKHTAlC8IQ_AUIBigB">q=count+bubbles+UI&amp;rlz=1C5CHFA_enUS503US503&amp;espv=2&amp;biw=1440&amp;bih=782&amp;source=lnms&amp;tbm=isch&amp;sa=X&amp;ved=0ahUKEwjS7v2Ig4LLAhVM8GMKHTAlC8IQ_AUIBigB) </a>next to each user name. The rst count bubble (colored green) should be the count of the number of photos the user has in the system. The second bubble (colored red) should be a count of the number of comments that the user has authored.

Clicking on the comment count bubble of a user should go to a new view component that shows all the comments of the user. For each of the user’s comments the view should show a small thumbnail of the photo on which the comment was made and the text of the comment. Clicking on the comment or photo should switch the view to photo’s detail view containing that photo and all its comments. The exact view will depend on if you implemented the stepper extra credit in project 5 or not.

This change should only be visible if the advanced feature ag of project 5 is enabled. If you didn’t do the extra credit of Project #5 you don’t need to do the stepper but you will still need to implement the advanced feature ag control so that the above extra credit functionality can be toggled on or o .

In implementing this you are welcome to add new server API calls or enhance existing calls. If you do so you need to update the Mocha test ( test/serverApiTest.js ) to test your new functionality. If you add new APIs include them in a new describe() block following the pattern used by the other tests. Make sure that the provided tests still pass before submitting. You should not add new properties to the Mongoose Schema but you are welcome to add any indexes you need to make this work on larger data sets.

For grading the course sta will enable the Advanced Features setting on your app (if present) and look for the count bubbles UI to determine whether or not they should grade you on the extra credit portion.

<h1> Style Points (5 points)</h1>

These points will be awarded if your problem solutions have proper MVC decomposition. In addition, your code and templates must be clean and readable, and your app must be at least “reasonably nice” in appearance and convenience.

In addition, your code and templates must be clean and readable. Remember to run npm run lint before submitting. The linter should raise no errors.

<h1> Deliverables</h1>

Use the standard class <a href="https://web.stanford.edu/class/cs142/submit.html">submission mechanism (submit.html)</a> to submit the entire project6 directory. Make sure your code is free of any lint warnings and passes the provided test suite.

Designed by Raymond Luong for CS142 at Stanford University

Powered by <a href="https://getbootstrap.com/">Bootstrap (http://getbootstrap.com/) </a>and <a href="https://jekyllrb.com/">Jekyll (https://jekyllrb.com)</a> – <a href="https://web.stanford.edu/class/cs142/website.html"><strong>learn more</strong></a><a href="https://web.stanford.edu/class/cs142/website.html"> (website.html)</a>
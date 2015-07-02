# AppEngine Launching An App

After the lesson, you'll know:
- How to launch an app on Appengine
- What is an app.yaml configuration file and how to create one
- Create a Python script to generate a response
- Code Along to Hello World  and run a basic web app on AppEngine
- Make edits to an app that has been launched

## Motivation / Why Should You Care?
We've learned about frontend technologies - the HTML, CSS, and Javascript files that get sent to the browser and are rendered as web pages. We've learned about python - a versatile language for implementing logic and solving problems. This week, we'll learn how to use python and Google's App Engine to create programs that serve web pages.

Today, we'll start by launching a simple Hello, World App, then zoom out and think about what App Engine really is and how the pieces fit together. We'll round the day off by looking at App Engine Templates, which will let us start to use our python logic in rendered html pages.

## Minimal Viable Product
![Bare Donut as minimum viable product, chocolate sprinkle donut as product](https://pando-assets.s3.amazonaws.com/uploads/2013/02/product_donuts-copy.jpg)

We are going to build a barebones web app so that we have a MVP to build off of. When learning software and building projects, it's often best to build the smallest thing that works, then grow from there. We'll start by building and launching the smallest App Engine project we can, and then grow from there.

1. Open up GoogleAppEngineLauncher
Create a new application by going to the File menu and picking "New Application..."

Call your application “appengine-practice”
Click the browse button to save the application in the desktop directory.  

Enter 8080 in the port field if it's not already there (Not admin port!)

      5. Click Create.
      6. Click the green Run button.
      7. Open a new google chrome browser and put the url: http://localhost:8080/
You should see: Hello, world!

## So What?
	CONGRATS you just made MVP app in Google AppEngine. Have a donut!

#### What is localhost?
It is a special alias that means your local computer.  For example if you try to go to http://localhost:8080 means that you're looking for a server that's running on a specific port (8080) of your local machine.

#### What is a port? Why 8080?
A port is kind of like an address that gives you a place to connect to a machine.  For lots more detail see Wikipedia's article on ports. Websites usually run on port 80 for http or port 443 for https, and App Engine uses the series 8080, 8081, 8082... by default for your apps that you run on localhost. 8080 is just a convention, but we could choose something else. If we choose randomly, another application might be using the port!

##What did AppEngine do for you?
We clicked a button, and it spit out some files. Let's take a look at those files and see what each of them are doing, so we know how to modify them to build our app.

Open app.yaml in atom. The app.yaml file is a configuration file. It tells appengine how to run our app!

It first tells appengine what language we’re writing in and the name of the app (which needs to be all lowercase with no underscores!)

Then it shows the handlers. The handlers tell App Engine which URLs should be directed to which Python programs. In this case, we direct any url to the application defined in main.py. We do this by pointing the handler to the variable app in the module main - main.app.

Finally, we have a place for our libraries. As the default, we need to include webapp2 which is a web application framework. Web frameworks are libraries that help with common tasks in building a website - webapp2 allows us to easily access our database, set up routes correctly, and render our views (which in AppEngine are called templates) and other useful utilities that web application developers would otherwise have to build for themselves every time.

```yaml
application: appengine-practice #unique application name
version: 1 # version 1 of this application’s code
runtime: python27 # running on python 2.7
api_version: 1 # API version 1
threadsafe: yes # how AppEngine processes multiple requests simultaneously

handlers:
- url: /favicon\.ico
  static_files: favicon.ico
  upload: favicon\.ico

- url: .*
  script: main.app

libraries:
- name: webapp2
  version: "2.5.2"
```

Every request to a URL whose path matches the regular expression /.* (all URLs) so this block means "send any URLs to main.py".

Open main.py in Atom

The simplest HTTP response consists of its type and content.
```python
import webapp2 #imports webapp2

class MainHandler(webapp2.RequestHandler):
    def get(self):
        self.response.write('Hello world!')

app = webapp2.WSGIApplication([
    ('/', MainHandler)
], debug=True)
```
Let’s look at each part in more detail. You may remember the webapp2 library from the app.yaml file. We use this to define handlers.
```python
import webapp2
```
Handlers are defined like this. Each handler is a class with one method that runs to handle the request and generate a response. This is typically called get, but we’ll see another example later on when we deal with forms.
```python
class MainHandler(webapp2.RequestHandler):
    def get(self):
        self.response.write('Hello world!')
```
The only thing the handler does right now is write a string (“Hello world!”) to the response, but you can add any Python here you like.

Finally, we need to tell the program which URLs map to which handlers. These are often called “routes”:

```python
app = webapp2.WSGIApplication([
    ('/', MainHandler)
], debug=True)
```
You may remember the app variable from app.yaml (i.e. main.app). This is what it refers to. This is how app engine knows which URLs should run which Python methods. Inside the app, we get another chance to choose where different routes will be handled. Here, we’re mapping the URL / to the MainHandler, which as we saw, will respond with 'Hello world!'.

##Creating a handler
Let’s try adding a new handler. There are typically two things to do:

Create a new handler class with a unique name
Add a new route mapping a URL to that handler.

To demonstrate that these handlers are just Python, let’s write a handler that counts to 100. Our new handler will have a for loop:

```python
class CountHandler(webapp2.RequestHandler):
    def get(self):
        for i in range(1, 101):
            self.response.write(i)
```
Now, add a route to map it:

```python
app = webapp2.WSGIApplication([
    ('/', MainHandler),
    (‘/count’, CountHandler),
], debug=True)
```
Test this in your browser at http://localhost:8080/count. (If your application is running on a different port number, make sure you use the correct one.)

Your complete main.py file should now look like this:

```python
import webapp2

class MainHandler(webapp2.RequestHandler):
    def get(self):
        self.response.write('Hello world!')

class CountHandler(webapp2.RequestHandler):
    def get(self):
        for i in range(1, 101):
            self.response.write(i)

app = webapp2.WSGIApplication([
    ('/', MainHandler),
    (‘/count’, CountHandler),
], debug=True)
```
Once again, your screen says: Hello, world!

It also counts to 100 when we change the url to /count. Our new handler at work!

Let’s not be so repetitive. We can continue to edit our application and see the updates immediately in the browser. Let’s try it out!

Edit Your Application:

1. Change the message in your helloworld.py script from "Hello, world!" to something else. Refresh the page to see the change.

2. Try changing text/plain to text/html. What changes? Why?

3. Your helloworld.py script can do anything that you'd expect a Python script to do. Try adding variables and expressions, like this:

```python
print 'Content-Type: text/plain'
print ''
print 'Hello, world!'

result = 2 + 2
print result
```

## 2

### 2.1

```
import os
from flask import Flask

# 1 We need to crate an instance of this class
app = Flask(__name__) # 1 the first argument= name of application module or package
                        # 1 __name__ = is built in python variable
                            # 1 Flask needs this so that it knows where to look for templates and static files


# 1 Tell the flask what route should trigger the function that follows
# 1 Done using the decorator
# 1 Decorator is a way of wrapping functions
# 1 When we try to browse to the root directory (as indicated by the forward slash)
    # 1 Flask triggers the function below 
@app.route("/")
def index():
    return "Hello, World"

print('hello world')

if __name__ == "__main__": # 1 the __main__ is name oof default module in python.
    app.run(
        host = os.environ.get("IP", "0.0.0.0"),
        port = int(os.environ.get("PORT", "5000")),
        debug = True
    )
    # 1 We use os module to 
        # 1 host = hostname to listen on 
            # 1 get IP environment variable if it exists, but set the default value if it's not found
        # 1 port = the port of the webserver
        # 1 debug = enable/disable debug mode
```

### 2.2

```
import os
from flask import Flask, render_template
#2 import render template function from flask
    #2 render_template = renders specified template
            #2 Flask expects templates to be in templates dir
            #2 -- on the same lvl as our python file

app = Flask(__name__) 


@app.route("/")
def index():
    return render_template("index.html")
```

### 2.3
```
    <nav>
        <ul>
            <!--<li><a href="{{ url_for('index')}}">Home</a></li>
                <li><a href="{{ url_for('about')}}">About</a></li>
                Jinja templating method url_for 
                    - calls appropriate functions 
                    - The names need to correspond to views/routes-->
            <li><a href="{{ url_for('index')}}">Home</a></li>
            <li><a href="{{ url_for('about')}}">About</a></li>
            <li><a href="{{ url_for('contact')}}">Contact</a></li>
            <li><a href="{{ url_for('careers')}}">Careers</a></li>
        </ul>
    </nav>
```

### 2.4
Base HTML
```
    <!-- block content = defines a block/area where we can inject content from other pages 
    - the difference between single curly brackets and double curly brackets is that double curly brackets contain an expression (which is outputting something)
        - the single curly brackets are for statements that control the flow of the wepage
    -->
    {% block content %}
    {% endblock %}
```
Index HTML
```
{% extends "base.html" %}
{% block content %}
    <h1>Home Page</h1>
{% endblock %}
```

## 4
```
@app.route("/about")
def about():
    # import json
    # we need to have python open the JSON file in order to read it, accomplished using "with" block
    # with open(...) = opens the JSON file as read only
    # we need to set empty 'data' list to the parsed json file that we sent through
    data = []
    with open("data/company.json", "r") as json_data:
        data = json.load(json_data)
    return render_template("about.html", page_title='About', company=data)
```

## 5
### 5.1
```
@app.route("/about/<member_name>")
def about_member(member_name):
    # Create a route for each individual member
    member = {}
    with open("data/company.json", "r") as json_data:
        data = json.load(json_data)
        for obj in data:
            if obj["url"] == member_name:
                member = obj
    return render_template("member.html", member=member)
```
### 5.3
```
# 1 If we want to submit data with "post" method, that needs to be explicitly declared in the route
# 2 we need to import request library
    # Request will handle stuff like finding out what method we used
    # - and will also contain our form object when we posted it
@app.route("/contact", methods=["GET", "POST"])
def contact():
    # we need to check out if the request method is indeed post
    if request.method == "POST":
        print(request.form)
    return render_template("contact.html", page_title='Contact')
```

### 5.4
Displaying flash messages

run.py

```
from flask import Flask, render_template, request, flash
if os.path.exists("env.py"):
    import env

app = Flask(__name__)
app.secret_key = os.environ.get("SECRET_KEY")
# 1 we need to import flash from flask to be able to flash a message to the user
# 2 we have to create .gitignore and env.py 
    # so we can hide sensitive data or environment variables
# 3 add required information into env.py file
# 4 add to gitignore files that should be ignored
# 5 now we have to import our env file, but only import if the system can find env.py file
# 6 pycache file is now imported and we don't need to import that either
# 7 now we want to grab our variable named SECRET_KEY and put it in app.secret_key



# 8 now we can call flash function
@app.route("/contact", methods=["GET", "POST"])
def contact():
    if request.method == "POST":
        flash("Thanks {}, we have received your message!".format(
            request.form.get("name")))
    return render_template("contact.html", page_title='Contact')
```

gitbash

```
marja@Shadowthrone MINGW64 /d/CODING/CODE INSTITUTE/materials/11 introduction to flask/projects/pr01
$ touch .gitignore

marja@Shadowthrone MINGW64 /d/CODING/CODE INSTITUTE/materials/11 introduction to flask/projects/pr01
$ touch env.py

marja@Shadowthrone MINGW64 /d/CODING/CODE INSTITUTE/materials/11 introduction to flask/projects/pr01
$
```

env.py
```
import os

# set the hidden variable
    # takes two arguments
        # 1. variable used to grab sensitive data
        # 2. second is the key itself or confidental db information
os.environ.setdefault("SECRET_KEY", "secret_flash_key")
```

gitignore
```
env.py
__pycache__/
myenv/
```

contact.html
```
        
        <!-- get_flashed_messages() will return any of 
        nessages created using Flash() function and store them in variable called messages -->
        {% with messages = get_flashed_messages() %}
            {% if messages %}
                <ul class="flashes">
                    {% for message in messages %}
                        <li>{{ message }}</li>
                    {% endfor %}
                </ul>
            {% endif %}
        {% endwith %}
        
```
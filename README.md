# ****Python Flask - Todo App****

Install Flask

for mac - 

`python3 -m venv .venv`

`. .venv/bin/activate`

`pip install Flask`

Flask - webframe in the python, complex webapp

Integrated terminal Cntrl +`

local server - http://127.0.0.1:5000/

Cntrl + C —> quit 

to run —> python app.py

app.py

```python
from flask import Flask, render_template, request, redirect, url_for
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)

app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///db.sqlite'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
db = SQLAlchemy(app)

class Todo(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100))
    complete = db.Column(db.Boolean)

@app.route("/")
def index():
    # show all todos
    todo_list = Todo.query.all()
    return render_template('base.html', todo_list=todo_list)

@app.route("/add", methods=["POST"])
def add():
    # add new items
    title = request.form.get("title")
    new_todo = Todo(title=title, complete=False)
    db.session.add(new_todo)
    db.session.commit()
    return redirect(url_for("index"))

@app.route("/update/<int:todo_id>")
def update(todo_id):
    # add new item
    todo = Todo.query.filter_by(id=todo_id).first()
    todo.complete = not todo.complete
    db.session.commit()
    return redirect(url_for("index"))

@app.route("/delete/<int:todo_id>")
def delete(todo_id):
    # add new item
    todo = Todo.query.filter_by(id=todo_id).first()
    db.session.delete(todo)
    db.session.commit()
    return redirect(url_for("index"))

if __name__=="__main__":
    with app.app_context():
        db.create_all()
        app.run(debug=True)
```

base HTML

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>todo app</title>

    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/semantic-ui@2.5.0/dist/semantic.min.css">
<script src="https://cdn.jsdelivr.net/npm/semantic-ui@2.5.0/dist/semantic.min.js"></script>
</head>
<body>
    <div style="margin-top: 50px;" class="ui container">
        <h1 class="ui center aligned header">Todo App</h1>
        <form class="ui form" action="/add", method="post">
        <div class="field">
            <label for ="title">Todo Title</labelfor>
            <input type="text" name="title" placeholder="Enter todo...">
            <br>
            <button class="ui blue button" type="submit">Add</button>
        </div>
    </form>
    {% for todo in todo_list %}
    <div class="ui segment">
        <p class="ui big header"> {{ todo.id }} | {{ todo.title }} </p>

        {% if todo.complete == False %}
            <span class="ui gray label">Not Completed</span>
        {% else %}
            <span class="ui green label">Completed</span>
        {% endif %}

        <a class="ui blue button" href="/update/{{ todo.id }}">Update</a>
        <a class ="ui red button" href="/delete/{{ todo.id }}">Delete</a>

    </div>

    {% endfor %}
</div>

</body>
</html>
```

Note: it should be saved under FLASK-TODO

base-html should be saved under the file name “templates”

for CSS used - semantic ui

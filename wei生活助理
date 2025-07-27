from flask import Flask, render_template, request, redirect, session, url_for, g
import sqlite3, os
from datetime import date

app = Flask(__name__)
app.secret_key = "your_secret_key"  # 用于 session 存储语言

# 初始化数据库
def init_db():
    if not os.path.exists("database.db"):
        conn = sqlite3.connect("database.db")
        c = conn.cursor()
        c.execute("""
            CREATE TABLE schedule (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                date TEXT,
                time TEXT,
                task TEXT
            )
        """)
        c.execute("""
            CREATE TABLE expenses (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                date TEXT,
                category TEXT,
                amount REAL
            )
        """)
        c.execute("""
            CREATE TABLE habits (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                date TEXT,
                wake_time TEXT,
                sleep_time TEXT
            )
        """)
        conn.commit()
        conn.close()

init_db()

# 语言切换功能
@app.before_request
def get_lang():
    lang = session.get("lang", "zh")
    g.lang = lang

@app.route("/lang")
def switch_lang():
    lang = request.args.get("lang", "zh")
    if lang not in ["zh", "en"]:
        lang = "zh"
    session["lang"] = lang
    return redirect(request.referrer or "/")

# 首页
@app.route("/")
def index():
    return f"""
    <html>
    <head><meta charset='utf-8'><title>{'生活助手' if g.lang == 'zh' else 'Life Assistant'}</title></head>
    <body>
        <h1>{'欢迎使用生活助手！' if g.lang == 'zh' else 'Welcome to Life Assistant!'}</h1>
        <a href='/schedule'>{'📅 安排日程' if g.lang == 'zh' else '📅 Schedule Task'}</a><br>
        <a href='/expense'>{'💰 记录消费' if g.lang == 'zh' else '💰 Track Expense'}</a><br>
        <a href='/sleep'>{'🌙 睡眠习惯' if g.lang == 'zh' else '🌙 Sleep Habit'}</a><br><br>
        <b>{'切换语言：' if g.lang == 'zh' else 'Switch Language:'}</b>
        <a href='/lang?lang=zh'>中文</a> | <a href='/lang?lang=en'>English</a>
    </body>
    </html>
    """

# 安排日程
@app.route("/schedule", methods=["GET", "POST"])
def schedule():
    if request.method == "POST":
        date_ = request.form["date"]
        time_ = request.form["time"]
        task = request.form["task"]
        conn = sqlite3.connect("database.db")
        c = conn.cursor()
        c.execute("INSERT INTO schedule (date, time, task) VALUES (?, ?, ?)", (date_, time_, task))
        conn.commit()
        conn.close()
        return redirect("/schedule")
    
    today = date.today().isoformat()
    return f"""
    <html>
    <head><meta charset='utf-8'><title>{'安排日程' if g.lang == 'zh' else 'Schedule Task'}</title></head>
    <body>
        <h2>{'📅 安排日程' if g.lang == 'zh' else '📅 Schedule Task'}</h2>
        <form method='post'>
            <label>{'日期' if g.lang == 'zh' else 'Date'}: <input type='date' name='date' value='{today}'></label><br>
            <label>{'时间' if g.lang == 'zh' else 'Time'}: <input type='time' name='time'></label><br>
            <label>{'任务内容' if g.lang == 'zh' else 'Task'}: <input type='text' name='task'></label><br>
            <button type='submit'>{'添加任务' if g.lang == 'zh' else 'Add Task'}</button>
        </form>
        <br><a href='/'>{'返回首页' if g.lang == 'zh' else 'Back to Home'}</a>
    </body>
    </html>
    """

# 记录消费
@app.route("/expense", methods=["GET", "POST"])
def expense():
    if request.method == "POST":
        date_ = request.form["date"]
        category = request.form["category"]
        amount = float(request.form["amount"])
        conn = sqlite3.connect("database.db")
        c = conn.cursor()
        c.execute("INSERT INTO expenses (date, category, amount) VALUES (?, ?, ?)", (date_, category, amount))
        conn.commit()
        conn.close()
        return redirect("/expense")
    
    today = date.today().isoformat()
    return f"""
    <html>
    <head><meta charset='utf-8'><title>{'记录消费' if g.lang == 'zh' else 'Track Expense'}</title></head>
    <body>
        <h2>{'💰 记录消费' if g.lang == 'zh' else '💰 Track Expense'}</h2>
        <form method='post'>
            <label>{'日期' if g.lang == 'zh' else 'Date'}: <input type='date' name='date' value='{today}'></label><br>
            <label>{'分类' if g.lang == 'zh' else 'Category'}: <input type='text' name='category'></label><br>
            <label>{'金额' if g.lang == 'zh' else 'Amount'}: <input type='number' step='0.01' name='amount'></label><br>
            <button type='submit'>{'添加消费' if g.lang == 'zh' else 'Add Expense'}</button>
        </form>
        <br><a href='/'>{'返回首页' if g.lang == 'zh' else 'Back to Home'}</a>
    </body>
    </html>
    """

# 睡眠习惯
@app.route("/sleep", methods=["GET", "POST"])
def sleep():
    if request.method == "POST":
        wake = request.form["wake"]
        sleep = request.form["sleep"]
        today = date.today().isoformat()
        conn = sqlite3.connect("database.db")
        c = conn.cursor()
        c.execute("INSERT INTO habits (date, wake_time, sleep_time) VALUES (?, ?, ?)", (today, wake, sleep))
        conn.commit()
        conn.close()
        return redirect("/sleep")
    
    return f"""
    <html>
    <head><meta charset='utf-8'><title>{'睡眠习惯' if g.lang == 'zh' else 'Sleep Habit'}</title></head>
    <body>
        <h2>{'🌙 睡眠习惯记录' if g.lang == 'zh' else '🌙 Sleep Habit Tracker'}</h2>
        <form method='post'>
            <label>{'起床时间' if g.lang == 'zh' else 'Wake-up Time'}: <input type='time' name='wake'></label><br>
            <label>{'入睡时间' if g.lang == 'zh' else 'Sleep Time'}: <input type='time' name='sleep'></label><br>
            <button type='submit'>{'记录睡眠' if g.lang == 'zh' else 'Record Sleep'}</button>
        </form>
        <br><a href='/'>{'返回首页' if g.lang == 'zh' else 'Back to Home'}</a>
    </body>
    </html>
    """

# 运行应用
if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)

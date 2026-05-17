# margadarshan-app
student app delopying learing 
import streamlit as st
import sqlite3
import pandas as pd

# --- DATABASE SETUP ---
def init_db():
    conn = sqlite3.connect('margdarsh.db')
    c = conn.cursor()
    # Create Tables based on your sketches
    c.execute('''CREATE TABLE IF NOT EXISTS mentors 
                 (id INTEGER PRIMARY KEY, name TEXT, email TEXT, password TEXT, dept TEXT, phone TEXT, qual TEXT, exp TEXT)''')
    c.execute('''CREATE TABLE IF NOT EXISTS students 
                 (id INTEGER PRIMARY KEY, name TEXT, email TEXT, password TEXT, dept TEXT, phone TEXT, prn TEXT, year TEXT, marks TEXT)''')
    conn.commit()
    conn.close()

init_db()

# --- APP LAYOUT ---
st.set_page_config(page_title="Margdarsh App", layout="wide")
st.title("🛡️ Margdarsh: Student Monitoring Hub")

menu = ["Login", "Mentor Registration", "Student Registration", "Admin View"]
choice = st.sidebar.selectbox("Menu", menu)

# --- REGISTRATION LOGIC ---
if choice == "Mentor Registration":
    st.header("Mentor Registration Form")
    with st.form("m_form"):
        name = st.text_input("Full Name")
        email = st.text_input("Email")
        pwd = st.text_input("Password", type='password')
        dept = st.selectbox("Department", ["CSE", "Mechanical", "Civil", "ETC"])
        phone = st.text_input("Phone Number")
        qual = st.text_area("Qualification")
        exp = st.text_input("Years of Experience")
        
        if st.form_submit_button("Register Mentor"):
            conn = sqlite3.connect('margdarsh.db')
            c = conn.cursor()
            c.execute("INSERT INTO mentors (name, email, password, dept, phone, qual, exp) VALUES (?,?,?,?,?,?,?)", 
                      (name, email, pwd, dept, phone, qual, exp))
            conn.commit()
            st.success(f"Welcome Prof. {name}! Registration Successful.")

elif choice == "Student Registration":
    st.header("Student Registration Form")
    with st.form("s_form"):
        name = st.text_input("Full Name")
        email = st.text_input("Email")
        pwd = st.text_input("Password", type='password')
        prn = st.text_input("PRN Number")
        dept = st.selectbox("Department", ["CSE", "Mechanical", "Civil", "ETC"])
        year = st.selectbox("Current Year", ["FE", "SE", "TE", "BE"])
        
        if st.form_submit_button("Register Student"):
            conn = sqlite3.connect('margdarsh.db')
            c = conn.cursor()
            c.execute("INSERT INTO students (name, email, password, dept, prn, year) VALUES (?,?,?,?,?,?)", 
                      (name, email, pwd, dept, prn, year))
            conn.commit()
            st.success(f"Student {name} (PRN: {prn}) Registered!")

# --- DASHBOARD / DATABASE VIEW ---
elif choice == "Admin View":
    st.header("Database Overview (Admin Only)")
    conn = sqlite3.connect('margdarsh.db')
    
    st.subheader("Registered Mentors")
    mentors_df = pd.read_sql_query("SELECT name, dept, email, exp FROM mentors", conn)
    st.table(mentors_df)
    
    st.subheader("Registered Students")
    students_df = pd.read_sql_query("SELECT name, prn, dept, year FROM students", conn)
    st.table(students_df)
    conn.close()

elif choice == "Login":
    st.info("Select a Registration page to add users, then check 'Admin View' to see the saved data.")
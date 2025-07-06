import streamlit as st
from datetime import date
from dateutil.relativedelta import relativedelta

# Title
st.title("🧓 Know Your Age")

# Session State Initialization
if "submitted" not in st.session_state:
    st.session_state.submitted = False
if "name" not in st.session_state:
    st.session_state.name = ""
if "dob" not in st.session_state:
    st.session_state.dob = date.today()
if "reset_counter" not in st.session_state:
    st.session_state.reset_counter = 0  # Used to force rerender

# Form
with st.form("age_form", clear_on_submit=False):
    name = st.text_input("Enter your name", value=st.session_state.name)
    
    # Use dynamic key to force re-render when reset_counter changes
    dob = st.date_input(
        "Select your Date of Birth (MM/DD/YYYY)",
        value=st.session_state.dob,
        min_value=date(1940, 1, 1),
        max_value=date.today(),
        format="MM/DD/YYYY",
        key=f"dob_{st.session_state.reset_counter}"
    )

    col1, col2 = st.columns(2)
    with col1:
        submit = st.form_submit_button("Submit")
    with col2:
        clear = st.form_submit_button("Clear")

# Button Logic
if submit:
    st.session_state.name = name
    st.session_state.dob = dob
    st.session_state.submitted = True

if clear:
    st.session_state.name = ""
    st.session_state.dob = date.today()
    st.session_state.submitted = False
    st.session_state.reset_counter += 1  # Triggers re-render of date input

# Show result
if st.session_state.submitted:
    if st.session_state.name:
        today = date.today()
        diff = relativedelta(today, st.session_state.dob)

        years = diff.years
        months = diff.months
        days = diff.days

        # Age Category
        if years <= 12:
            category = "Child"
        elif 13 <= years <= 19:
            category = "Teenager"
        elif 20 <= years <= 48:
            category = "Adult"
        elif 49 <= years <= 100:
            category = "Senior Citizen"
        else:
            category = "Invalid Age"

        st.success(
            f"Hello {st.session_state.name}!\n\n"
            f"You are **{years} years**, **{months} months**, and **{days} days** old.\n\n"
            f"Category: **{category}**"
        )
    else:
        st.warning("Please enter your name.")

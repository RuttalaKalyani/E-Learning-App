from flask import Flask, jsonify, request

app = Flask(__name__)

# --- Simplified In-Memory Data (simulating a database) ---
# In a real app, this would come from a database like PostgreSQL, MongoDB, etc.

users_db = {
    "user123": {
        "username": "alice_learner",
        "email": "alice@example.com",
        "registered_courses": ["math_basics", "science_intro"]
    },
    "user456": {
        "username": "bob_student",
        "email": "bob@example.com",
        "registered_courses": ["history_101"]
    }
}

courses_db = {
    "math_basics": {
        "title": "Introduction to Basic Mathematics",
        "description": "Learn fundamental math concepts for free!",
        "price": "Free",
        "tutor": "Professor Sharma"
    },
    "science_intro": {
        "title": "General Science for Beginners",
        "description": "Explore the basics of physics, chemistry, and biology.",
        "price": "Free",
        "tutor": "Dr. Khan"
    },
    "history_101": {
        "title": "World History: Ancient Civilizations",
        "description": "Dive into the fascinating past of early human societies.",
        "price": "Free",
        "tutor": "Ms. Lee"
    },
    "premium_ai": {
        "title": "Advanced AI Concepts",
        "description": "A deep dive into neural networks (premium content).",
        "price": "Paid",
        "tutor": "Dr. Turing"
    }
}

# --- API Endpoints ---

@app.route('/')
def hello_world():
    """A simple greeting to confirm the server is running."""
    return "Welcome to the E-Learning App Backend (Basic)! " \
           "Try /profile/<user_id> or /courses/free"

@app.route('/profile/<user_id>', methods=['GET'])
def get_user_profile(user_id):
    """
    Returns a user's profile and their associated free courses.
    In a real app, 'user_id' would come from an authenticated session.
    """
    user_data = users_db.get(user_id)

    if not user_data:
        return jsonify({"message": "User not found"}), 404

    # Get details of registered courses that are free
    enrolled_free_courses = []
    for course_id in user_data["registered_courses"]:
        course_info = courses_db.get(course_id)
        if course_info and course_info.get("price") == "Free":
            enrolled_free_courses.append(course_info)

    # Prepare the response data
    profile_response = {
        "username": user_data["username"],
        "email": user_data["email"],
        "enrolled_free_courses": enrolled_free_courses
    }
    return jsonify(profile_response)

@app.route('/courses/free', methods=['GET'])
def get_all_free_courses():
    """
    Returns a list of all available free courses.
    """
    free_courses_list = []
    for course_id, course_details in courses_db.items():
        if course_details.get("price") == "Free":
            free_courses_list.append({
                "id": course_id,
                "title": course_details["title"],
                "description": course_details["description"],
                "tutor": course_details["tutor"]
            })
    return jsonify({"free_courses": free_courses_list})


# --- How to Run This Code ---
if __name__ == '__main__':
    # Run the Flask development server
    # It will be accessible at http://127.0.0.1:5000/
    app.run(debug=True)
    

# Student Report System

## Overview

This Python program is a student management system that allows you to store, retrieve, search, delete, and update student information in a text file. It's a command-line application, providing a menu-driven interface for performing various operations.  The program now includes robust input validation and error handling.

## Features

* **Add Student:** Add a new student's information (name, age, and marks) to the `students.txt` file. Input validation ensures that the provided data is of the correct type and within reasonable ranges using regular expressions.
* **View All Students:** Display the information of all students currently stored in the `students.txt` file. The program calculates and displays a basic result (Excellent, Good, Pass, or Fail) based on the student's marks.
* **Search Student by Name:** Search for a student by their name. If found, the program displays the student's details and result.
* **Delete Student by Name:** Delete a student's record from the `students.txt` file based on their name.
* **Update Student by Name:** Update a student's age and/or marks in the `students.txt` file based on their name.
* **Exit:** Terminate the program.

## How to Run

1.  **Prerequisites:** Python 3.x must be installed on your system.
2.  **Clone the Repository:** (If you are using Git)

    ```bash
    git clone <repository_url>
    cd student-report-system  # Or the name of the directory
    ```
3.  **Run the program:**

    ```bash
    python main.py
    ```

    (Replace `main.py` with the actual name of your Python script if it's different).
4.  **Interact with the Menu:** Follow the on-screen menu prompts to perform the desired operations.

## Code Explanation

Here's a breakdown of the Python code:

```python
import re  # Import the regular expression module

def get_student_info() -> str:
    """
    Prompts the user to enter student information (name, age, marks)
    and returns the information as a formatted string.
    Includes robust input validation using regular expressions.

    Returns:
        str: A string containing the student's name, age, and marks,
             separated by commas, and ending with a newline character.
             Returns an empty string if no information is entered.
    """
    while True:
        name = input("Enter Student's Name: ").strip()
        if not name:
            print("❌ Name cannot be empty")
            continue  # Go back to the beginning of the while loop

        # Use regular expression for age validation (ensure it's a number >= 5)
        age = input("Enter Student's Age: ").strip()
        if not re.match(r"^\d+$", age) or int(age) < 5:
            print("❌ Invalid age. Must be a number >= 5.")
            continue

        # Use regular expression for marks validation (ensure it's a number between 0 and 100)
        marks = input("Enter Student's Marks: ").strip()
        if not re.match(r"^\d+$", marks) or not (0 <= int(marks) <= 100):
            print("❌ Invalid marks. Must be a number between 0 and 100.")
            continue

        return f"{name},{age},{marks}\n"
get_student_info() Function:This function gathers student details (name, age, and marks) from the user.It uses a while True loop to continuously prompt for input until valid data is entered.strip() removes leading/trailing whitespace from the input.Input validation is performed:Name cannot be empty.Age must be a digit and greater than or equal to 5.  This is checked using the regular expression r"^\d+$".Marks must be a digit and between 0 and 100 (inclusive). This is checked using the regular expression r"^\d+$".If any input is invalid, an error message is printed, and the continue statement restarts the loop.The function returns the validated data as a comma-separated string with a newline character at the end, suitable for writing to a file.def save_student(line: str, file_name: str = "students.txt") -> None:
    """
    Appends a student's information to a file.  Handles potential errors.

    Args:
        line (str): The student's information as a comma-separated string
                      (e.g., "John Doe,20,85\n").
        file_name (str, optional): The name of the file to save to.
                      Defaults to "students.txt".
    Returns:
        None
    """
    try:
        with open(file_name, "a") as file:
            file.write(line)
    except (IOError, OSError) as e:
        print(f"❌ Error writing to file '{file_name}': {e}")
        # Consider re-raising the exception or returning a specific error code/message
        # raise  # Option 1: Re-raise the exception
        # return 1 #option 2: return error code
save_student() Function:This function saves the student's information to a file.It takes the student data (line) and an optional file_name (defaulting to "students.txt").The with open(file_name, "a") as file: statement opens the file in append mode ("a"), which means new data is added to the end of the file without overwriting existing content. The with statement ensures the file is automatically closed, even if errors occur.file.write(line) writes the student's information (including the newline character) to the file.The function includes a try...except block to catch IOError or OSError exceptions that might occur during the file writing process. If an error occurs, it prints an error message.def read_students(file_name: str = "students.txt") -> list[str]:
    """
    Reads all student information from a file.  Handles file not found and other errors.

    Args:
        file_name (str, optional): The name of the file to read from.
            Defaults to "students.txt".

    Returns:
        list[str]: A list of strings, where each string represents
                     a line from the file (i.e., one student's record).
                     Returns an empty list if the file does not exist or is empty,
                     or if an error occurs.
    """
    try:
        with open(file_name, 'r') as file:
            return file.readlines()
    except FileNotFoundError:
        print(f"❌ File '{file_name}' not found. Returning empty list.")
        return []  # Return an empty list if the file doesn't exist
    except (IOError, OSError) as e:
        print(f"❌ Error reading from file '{file_name}': {e}")
        return []  # Return empty list in case of other errors.  Consider re-raising.
        # raise  #option to re-raise
read_students() Function:This function reads student information from a file.It takes an optional file_name (defaulting to "students.txt").The with open(file_name, 'r') as file: statement opens the file in read mode ("r").file.readlines() reads all lines from the file and returns them as a list of strings. Each string in the list represents one line from the file, corresponding to one student's record.The function includes a try...except block to handle potential errors:FileNotFoundError: If the file does not exist, it prints a message and returns an empty list.IOError or OSError: For other file-related errors, it prints an error message and returns an empty list.def process_student_line(line: str) -> None:
    """
    Processes a single line of student data, extracts the information,
    and prints the student's name, age, marks, and result.
    Handles potential errors during data conversion.

    Args:
        line (str): A string containing the student's information
                      in comma-separated format (e.g., "John Doe,20,85\n").
    Returns:
       None
    """
    try:
        name, age, marks_str = line.strip().split(",")
        age = int(age)
        marks = int(marks_str)  # Convert marks to integer *before* further use
    except ValueError:
        print(f"❌ Error: Invalid data format in line: '{line.strip()}'. Skipping.")
        return  # Skip processing this line if there's a format error

    print(f"Name: {name}")
    print(f"Age: {age}")
    print(f"Marks: {marks}")

    if marks >= 90:
        print("Result: Excellent")
    elif marks >= 75:
        print("Result: Good")
    elif marks >= 50:
        print("Result: Pass")
    else:
        print("Result: Fail")
    print("-" * 40)  # Print a separator line
process_student_line() Function:This function takes a single line of student data as input.line.strip() removes any leading/trailing whitespace from the line.line.split(",") splits the line into a list of strings, using the comma as a delimiter. This separates the name, age, and marks.The function attempts to convert the age and marks to integers using int().A try...except block is used to catch ValueError exceptions that may occur if the age or marks are not valid integers. If a ValueError occurs, an error message is printed, and the function returns, skipping the rest of the processing for that line.The function prints the extracted name, age, and marks.The function determines the student's result (Excellent, Good, Pass, or Fail) based on their marks and prints the result.Finally, it prints a separator line ("-" * 40) for better readability.def search_student_by_name() -> None:
    """
    Searches for a student by name in the 'students.txt' file.
    Prompts the user for the name to search.  If a match is found,
    the student's information is displayed using process_student_line().
    Handles the case where the file is empty.
    """
    name_to_find = input("Enter name to search: ").strip().lower()
    students = read_students()

    if not students:
        print("❌ No student records found to search.")
        return

    found = False
    for line in students:
        name, age, marks = line.strip().split(",")
        if name.strip().lower() == name_to_find:
            print("\n🎯 Student Found:")
            process_student_line(line)
            found = True
            break  # Exit the loop after finding the student

    if not found:
        print("❌ Student not found.")
search_student_by_name() Function:Prompts the user to enter the name of the student to search for.Reads all student records from the "students.txt" file using read_students().Checks if the list of students is empty. If it is, it prints a message and returns.Initializes a found variable to False.Iterates through each line (student record) in the students list.For each line:Splits the line into name, age, and marks.Converts the student name and the search name to lowercase using .lower() for case-insensitive comparison.If a match is found:Prints a "Student Found" message.Calls process_student_line() to display the student's details.Sets found to True.break statement exits the loop, as the student has been found.If the loop finishes without finding a match (i.e., found is still False), it prints a "Student not found" message.def delete_student_by_name() -> None:
    """
    Deletes a student's record from the 'students.txt' file based on their name.
    Prompts the user for the name of the student to delete.  Handles edge cases
    like empty file and file errors.
    """
    name_to_delete = input("Enter the name of the student to delete: ").strip().lower()
    students = read_students()

    if not students:
        print("❌ No student records found to delete.")
        return

    updated_students = []
    found = False
    for line in students:
        name, age, marks = line.strip().split(",")
        if name.strip().lower() != name_to_delete:
            updated_students.append(line)  # Keep this student's record
        else:
            found = True  # Student found, but not added to updated_students

    if found:
        try:
            with open("students.txt", "w") as file:
                file.writelines(updated_students)  # Overwrite the file
            print(f"✅ Student '{name_to_delete}' has been deleted.")
        except (IOError, OSError) as e:
            print(f"❌ Error writing to file: {e}")
            print("❌ Deletion failed.") # Inform the user that deletion failed.
    else:
        print("❌ Student not found.")
delete_student_by_name() Function:Prompts the user for the name of the student to delete.Reads all student records from the "students.txt" file.Checks if the list of students is empty. If it is, it prints a message and returns.Creates an empty list updated_students to store the records that should be kept.Iterates through the student records:If a student's name does not match the name to delete, their record is appended to the updated_students list.If a student's name does match, the found flag is set to True, but the record is not added to updated_students.If found is True (meaning the student was found and "deleted"):The original "students.txt" file is opened in write mode ("w"), which overwrites the entire file with the contents of updated_students.A success message is printed.Handles potential IOError and OSError exceptions during file writing. If an error occurs, it prints an error message.If found is False, a "Student not found" message is printed.def update_student_by_name() -> None:
    """
    Updates the age and/or marks of a student in the 'students.txt' file based on their name.
    Prompts the user for the name of the student to update, and then prompts for new age and marks.
    Handles cases where the file is empty or the student is not found.
    """
    name_to_update = input("Enter the name of the student to update: ").strip().lower()
    students = read_students()

    if not students:
        print("❌ No student records found to update.")
        return

    updated_students = []
    found = False

    for line in students:
        name, age, marks = line.strip().split(",")
        if name.strip().lower() == name_to_update:
            found = True
            print(f"Current age: {age}, Current marks: {marks}")
            new_age = input("Enter new age (or press Enter to keep same): ").strip()
            new_marks = input("Enter new marks (or press Enter to keep same): ").strip()

            if new_age:
                if re.match(r"^\d+$", new_age) and int(new_age) >= 5:
                    age = new_age
                else:
                    print("❌ Invalid age. Keeping old value.")

            if new_marks:
                if re.match(r"^\d+$", new_marks) and 0 <= int(new_marks) <= 100:
                    marks = new_marks
                else:
                    print("❌ Invalid marks. Keeping old value.")

            updated_line = f"{name},{age},{marks}\n"
            updated_students.append(updated_line)
        else:
            updated_students.append(line)

    if found:
        try:
            with open("students.txt", "w") as file:
                file.writelines(updated_students)
            print(f"✅ Student '{name_to_update}' updated successfully.")
        except (IOError, OSError) as e:
            print(f"❌ Error writing to file: {e}")
            print("❌ Update failed")
    else:
        print("❌ Student not found.")
update_student_by_name() Function:Prompts the user for the name of the student to update.Reads student records from the file.Checks if the list of students is empty. If it is, it prints a message and returns.Iterates through the records:If a name matches:Sets found to True.Displays the current age and marks.Prompts for a new age and new marks. If the user enters nothing (just presses Enter), the original age/marks are kept.Validates the new age and marks if they are provided, using regular expressions.Constructs the updated student record string.Appends the updated record to updated_students.If a name does not match, the original record is appended to updated_students.If the student was found:Overwrites the file with the updated records.Prints a success message.Handles potential IOError and OSError exceptions during file writing. If an error occurs, it prints an error message.If the student was not found, prints an error.def main_menu() -> None:
    """
    Displays the main menu and handles user interaction.
    This is the entry point of the program.  Implements a loop
    to keep the menu running.
    """
    while True:
        print("\n📋 Student Report System")
        print("1. Add Student")
        print("2. View All Students")
        print("3. Exit")
        print("4. Search Student by Name")
        print("5. Delete Student by Name")
        print("6. Update Student by Name")

        choice = input("Enter your choice (1/2/3/4/5/6): ").strip()

        if choice == "1":
            student_info = get_student_info()
            save_student(student_info)
            print("✅ Student saved successfully!")
        elif choice == "2":
            print("\n📄 All Student Records")
            students = read_students()
            if not students:
                print("No student records found.")
            else:
                for line in students:
                    process_student_line(line)
        elif choice == "3":
            print("👋 Exiting the program. Goodbye!")
            break
        elif choice == "4":
            search_student_by_name()
        elif choice == "5":
            delete_student_by_name()
        elif choice == "6":
            update_student_by_name()
        else:
            print("❌ Invalid choice. Please select a number from the menu.")



if __name__ == "__main__":
    main_menu()  # Call the main function to start the program
main_menu() Function:This function displays the main menu of the program and handles user input.It uses a while True loop to keep displaying the menu until the user chooses to exit.The menu options are:1: Add Student (calls get_student_info() and save_student())2: View All Students (calls read_students() and process_student_line())3: Exit (breaks the loop)4: Search Student by Name (calls search_student_by_name())5: Delete Student by Name (calls delete_student_by_name())6: Update Student by Name (calls update_student_by_name())The user's input is taken using input() and stripped of whitespace.An if-elif-else block is used to determine which function to call based on the user's choice.If the user enters an invalid choice, an error message is displayed.if __name__ == "__main__"::This ensures that the main_menu() function is called only when the script is run directly, and not when imported as a module.File Structuremain.py: The main Python script containing the code.students.txt: A text file (automatically created) used to store student data. Each line in this file represents one student's record, with the name, age, and marks separated by commas.AssumptionsThe program assumes that the user will enter valid data when prompted. While there is input validation, the program may not be completely robust to all types of unexpected input.The program uses a simple text file (students.txt) for data storage. For more robust and scalable applications, a database would be more appropriate.The program does not handle concurrent access to the students.txt file. If multiple users or processes try to modify the file at the same time, data corruption could occur.Potential ImprovementsData Validation: The input validation could be made more sophisticated.Data Storage: Consider using a database (like SQLite) instead of a plain text file for more robust data storage.User Interface: A graphical user interface (GUI) could be created using a library like Tkinter, PyQt, or Kivy to make the program more user-friendly.Modularity: The code could be further modularized by breaking it down into more functions or classes.Comments: Adding more comments to the code would improve its readability and maintainability.Logging: Implementing logging would help in debugging and tracking program usage.Testing: Writing unit tests would help ensure the program's correctness and prevent regressions.Sorting: Add functionality to sort the student records (e.g., by name, age, or marks).

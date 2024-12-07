# math153-final-project (ssstaw)

# Colors for text used throughout the journal
GREEN = '\033[32m'
ORANGE = '\033[33m'
CYAN = '\033[36m'
RED = '\033[31m'
PURPLE = '\033[35m'
RESET = '\033[0m'
BOLD = '\033[01m'

# Import required modules
import json  # Used for saving and loading data in JSON format
from datetime import datetime  # Used for handling and formatting dates

# File name where journal data is stored
JOURNAL_FILE = "shhh_akin_lang.json"

# Function to load journal entries from the JSON file
def load_entries():
    try:
        with open(JOURNAL_FILE, "r") as file:
            return json.load(file)
    except (FileNotFoundError, json.JSONDecodeError):
        return {"user_name": "", "entries": {}}  # Default structure if file not found or invalid

# Function to save the journal data to the JSON file
def save_entries(data):
    with open(JOURNAL_FILE, "w") as file:
        json.dump(data, file, indent=4)

# Function to set up the user's name if not already saved
def setup_user(data):
    if not data["user_name"]:
        user_name = input(f"{PURPLE}What's your name? {RESET}").capitalize()
        if user_name.lower() == "exit":  # Allow exiting during setup
            print(f"{CYAN}Exited setup. Balik ka na lang kapag ready ka na!{RESET}")
            exit()
        data["user_name"] = user_name
        save_entries(data)
        print(f"\n{BOLD}{GREEN}Hello, {user_name}! Umpisahan mo na ang chikka!!🤩{RESET}")
    else:
        print(f"\n{BOLD}{CYAN}Welcome back, {data['user_name']}! What's the ganap?{RESET}")

# Function to add a new journal entry with an option to return to the main menu
def add_entry(data):
    user_name = data["user_name"]
    entries = data["entries"]
    date = datetime.now().strftime("%m-%d-%Y")
    print(f"\n{BOLD}{CYAN}The new kwento for {date}:{RESET}")
    content = input(f"{PURPLE}Sulat mo dito sama ng loob mo teh (type 'exit' to cancel):\n{RESET}")
    if content.lower() == 'exit':  # Allow the user to cancel the process
        print(f"{CYAN}Ay, hindi ka pala magsusulat {user_name}. Okay, balik tayo sa menu.{RESET}")
        return
    if date not in entries:
        entries[date] = []
    entries[date].append(content)
    save_entries(data)
    print(f"{GREEN}Okay, ngayon alam ko na!🫢{RESET}")

# Function to view all journal entries with a return option
def view_entries(data):
    user_name = data["user_name"]
    entries = data["entries"]
    if not entries:
        print(f"{RED}Ay! Wala akong makita teh, magsulat ka kaya muna {user_name}.{RESET}")
        return
    print(f"\n{BOLD}{CYAN}Master {user_name}, ito na mga sama ng loob mo:{RESET}")
    for date, entry_list in sorted(entries.items()):
        print(f"\n{BOLD}{CYAN}Date: {date}{RESET}")
        for index, entry in enumerate(entry_list, 1):
            print(f"  {index}. {entry}")
    input(f"\n{CYAN}Press Enter to go back to the main menu...{RESET}")

# Function to search journal entries by keyword with an exit option
def search_entries(data):
    user_name = data["user_name"]
    entries = data["entries"]
    keyword = input(f"{ORANGE}Boss, ano hanap natin? Sulat mo lang dito (type 'exit' to cancel): {RESET}").lower()
    if keyword == 'exit':  # Allow the user to cancel the process
        print(f"{CYAN}Okay, di pala tayo maghahanap. Balik tayo sa menu.{RESET}")
        return
    found = False
    for date, entry_list in entries.items():
        for entry in entry_list:
            if keyword in entry.lower():
                print(f"\n{BOLD}{CYAN}Date: {date}{RESET}")
                print(f"  {entry}")
                found = True
    if not found:
        print(f"{RED}Ay, si gawagawa ka naman {user_name}, wala ka namang entry na ganito eh.{RESET}")

# Function to remove a specific journal entry with a return option
def remove_entry(data):
    user_name = data["user_name"]
    entries = data["entries"]
    if not entries:
        print(f"{RED}Wala ka namang entry para ma remove, anteh {user_name}.{RESET}")
        return
    
    print(f"\n{BOLD}{CYAN}{user_name}, ang mga nakikita mo ay mga chismis galing sayo:{RESET}")
    for date, entry_list in sorted(entries.items()):
        print(f"\n{BOLD}{CYAN}Date: {date}{RESET}")
        for index, entry in enumerate(entry_list, 1):
            print(f"  {index}. {entry}")
    
    date = input(f"{ORANGE}Gusto mo burahin? Enter the date of the entry you want to remove (MM-DD-YYYY) or type 'exit' to cancel: {RESET}")
    if date.lower() == 'exit':  # Allow the user to cancel the process
        print(f"{CYAN}Ay, di mo pala gusto magbura {user_name}. Geh, balik.{RESET}")
        return
    
    if date not in entries:
        print(f"{RED}No chismis found for that date, {user_name}.{RESET}")
        return
    
    try:
        entry_list = entries[date]
        for index, entry in enumerate(entry_list, 1):
            print(f"  {index}. {entry}")
        entry_index = input(f"{ORANGE}Enter the number of the entry to remove or type 'exit' to cancel: {RESET}")
        
        if entry_index.lower() == 'exit':  # Allow the user to cancel the process
            print(f"{CYAN}Ay, di mo pala gusto magbura {user_name}. Okay, balik tayo.{RESET}")
            return
        
        entry_index = int(entry_index) - 1
        
        if 0 <= entry_index < len(entry_list):
            removed_entry = entry_list.pop(entry_index)
            print(f"{GREEN}Wala na to: {removed_entry}{RESET}")
            if not entry_list:
                del entries[date]
            save_entries(data)
        else:
            print(f"{RED}Ay walang ganyang entry number, {user_name}.{RESET}")
    except ValueError:
        print(f"{RED}Luh, number beh. NUMBER. Please enter a number, {user_name}.{RESET}")

# Main program loop
def main():
    data = load_entries()
    setup_user(data)

    while True:  # Keep the program running until the user exits
        user_name = data["user_name"]
        print(f"\n{BOLD}{CYAN}What would you like to do, {user_name}?{RESET}")
        print(f"\n{BOLD}{GREEN}a. Add a new entry ✍🏼{RESET}")
        print(f"{BOLD}{GREEN}b. View all entries 📖{RESET}")
        print(f"{BOLD}{GREEN}c. Search entries by keyword 🔎{RESET}")
        print(f"{BOLD}{GREEN}d. Remove an entry 🗑️{RESET}")
        print(f"{BOLD}{RED}e. Exit 👋🏻{RESET}")
        choice = input(f"\n{ORANGE}Choose an option: {RESET}")

        if choice.lower() == "a":
            add_entry(data)
        elif choice.lower() == "b":
            view_entries(data)
        elif choice.lower() == "c":
            search_entries(data)
        elif choice.lower() == "d":
            remove_entry(data)
        elif choice.lower() == "e":
            print(f"{BOLD}{PURPLE}Good bye, {user_name}! Balik ka beh, gusto ko pa ng chikka galing sayo. See you next time!✨{RESET}")
            break
        else:
            print(f"{RED}Nasa choices ba yan? Wala diba? Ayusin mo naman, {user_name}. Ulit.{RESET}")

# Entry point of the program
if __name__ == "__main__":
    main()

import os
import subprocess
import datetime
import webbrowser
import psutil
import re


class TextAssistant:
    def _init_(self):  # Fixed constructor
        self.notes = []
        print("🤖 Text Desktop Assistant Started!")
        print("Type 'help' for available commands or 'quit' to exit.\n")

    def run(self):
        while True:
            try:
                command = input("You: ").strip().lower()
                if command in ['quit', 'exit', 'bye']:
                    print("Assistant: Goodbye!")
                    break
                self.process_command(command)
            except KeyboardInterrupt:
                print("\nAssistant: Goodbye!")
                break

    def process_command(self, cmd):
        # Same code as before...
        # (no need to paste again, everything else looks fine)

        if 'time' in cmd:
            time_now = datetime.datetime.now().strftime("%I:%M %p")
            print(f"Assistant: Current time is {time_now}")

        elif 'date' in cmd:
            date_now = datetime.datetime.now().strftime("%A, %B %d, %Y")
            print(f"Assistant: Today is {date_now}")

        elif 'system' in cmd or 'info' in cmd:
            cpu = psutil.cpu_percent(interval=1)
            memory = psutil.virtual_memory().percent
            print(f"Assistant: CPU: {cpu}%, Memory: {memory}%")

        elif 'battery' in cmd:
            try:
                battery = psutil.sensors_battery()
                if battery:
                    print(f"Assistant: Battery: {battery.percent}%")
                else:
                    print("Assistant: Battery info not available")
            except Exception as e:
                print(f"Assistant: Battery info error: {e}")

        elif 'open' in cmd:
            if 'notepad' in cmd:
                os.system('notepad' if os.name == 'nt' else 'gedit')
                print("Assistant: Opening notepad")
            elif 'calculator' in cmd or 'calc' in cmd:
                os.system('calc' if os.name == 'nt' else 'gnome-calculator')
                print("Assistant: Opening calculator")
            elif 'browser' in cmd:
                webbrowser.open('https://google.com')
                print("Assistant: Opening browser")
            elif 'files' in cmd or 'explorer' in cmd:
                os.system('explorer' if os.name == 'nt' else 'nautilus')
                print("Assistant: Opening file explorer")

        elif 'search' in cmd:
            query = cmd.replace('search', '').strip()
            if query:
                webbrowser.open(f"https://google.com/search?q={query}")
                print(f"Assistant: Searching for: {query}")
            else:
                print("Assistant: What do you want to search?")

        elif 'calculate' in cmd or 'math' in cmd:
            try:
                expression = cmd.replace('calculate', '').replace('math', '').strip()
                expression = expression.replace('plus', '+').replace('minus', '-')
                expression = expression.replace('times', '*').replace('divided by', '/')
                clean_expr = re.sub(r'[^\d+\-*/().\s]', '', expression)
                if clean_expr:
                    result = eval(clean_expr)
                    print(f"Assistant: {clean_expr} = {result}")
                else:
                    print("Assistant: Please provide a valid math expression")
            except Exception as e:
                print(f"Assistant: Invalid calculation: {e}")

        elif 'add note' in cmd or 'take note' in cmd:
            note = cmd.replace('add note', '').replace('take note', '').strip()
            if note:
                self.notes.append(f"{datetime.datetime.now().strftime('%H:%M')} - {note}")
                print("Assistant: Note added!")
            else:
                print("Assistant: What note do you want to add?")

        elif 'show notes' in cmd or 'list notes' in cmd:
            if self.notes:
                print("Assistant: Your notes:")
                for i, note in enumerate(self.notes, 1):
                    print(f"  {i}. {note}")
            else:
                print("Assistant: No notes found")

        elif 'clear notes' in cmd:
            self.notes.clear()
            print("Assistant: All notes cleared")

        elif 'list files' in cmd:
            try:
                files = os.listdir('.')
                print(f"Assistant: Found {len(files)} items:")
                for f in files[:10]:
                    print(f"  - {f}")
                if len(files) > 10:
                    print(f"  ... and {len(files) - 10} more")
            except Exception as e:
                print(f"Assistant: Error listing files: {e}")

        elif 'create folder' in cmd:
            folder_name = f"NewFolder_{datetime.datetime.now().strftime('%H%M%S')}"
            try:
                os.makedirs(folder_name, exist_ok=True)
                print(f"Assistant: Created folder: {folder_name}")
            except Exception as e:
                print(f"Assistant: Couldn't create folder: {e}")

        elif 'shutdown' in cmd:
            print("Assistant: Shutdown command received (not executed for safety).")

        elif 'help' in cmd:
            self.show_help()

        else:
            print("Assistant: I don't understand that command. Type 'help' for available commands.")

    def show_help(self):
        help_text = """
Available Commands:
* time - Get current time
* date - Get current date  
* system info - Show CPU and memory usage
* battery - Check battery status
* open notepad/calculator/browser/files - Open applications
* search [query] - Google search
* calculate [expression] - Do math
* add note [text] - Add a note
* show notes - Display all notes
* clear notes - Delete all notes
* list files - Show files in current directory
* create folder - Make a new folder
* help - Show this help
* quit - Exit assistant
        """
        print(help_text)


# Fixed main block
if _name_ == "_main_":
    assistant = TextAssistant()
    assistant.run()

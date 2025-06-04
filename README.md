# Alarm-appimport tkinter as tk
from tkinter import messagebox
import random
import datetime
import threading
import time

class AlarmApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Alarm App")

        self.alarm_time = tk.StringVar()
        self.alarm_status = tk.StringVar(value="Off")
        self.water_intake = [random.randint(50, 500) for _ in range(10)]
        self.sleep_time = [random.randint(6, 9) for _ in range(10)]

        # Alarm input
        tk.Label(root, text="Set alarm time (HH:MM)").grid(row=0, column=0)
        tk.Entry(root, textvariable=self.alarm_time).grid(row=0, column=1)
        tk.Button(root, text="Set Alarm", command=self.set_alarm).grid(row=0, column=2)

        # Alarm status
        tk.Label(root, text="Alarm status").grid(row=1, column=0)
        self.alarm_switch = tk.Label(root, textvariable=self.alarm_status, fg="red")
        self.alarm_switch.grid(row=1, column=1)
        tk.Button(root, text="Toggle Alarm", command=self.toggle_alarm).grid(row=1, column=2)

        # Graphs and Info
        tk.Button(root, text="View Water Intake Graph", command=self.show_water_graph).grid(row=2, column=0)
        tk.Button(root, text="View Sleep Data Graph", command=self.show_sleep_graph).grid(row=2, column=1)
        tk.Button(root, text="Why is Drinking Water Important?", command=self.show_water_info).grid(row=3, column=0)
        tk.Button(root, text="Why is Sleeping Important?", command=self.show_sleep_info).grid(row=3, column=1)

        # Start background alarm check
        self.check_alarm_thread = threading.Thread(target=self.check_alarm)
        self.check_alarm_thread.daemon = True
        self.check_alarm_thread.start()

    def set_alarm(self):
        alarm_time = self.alarm_time.get()
        if alarm_time:
            messagebox.showinfo("Alarm Set", f"Alarm set for {alarm_time}")
        else:
            messagebox.showwarning("Input Error", "Please enter a valid alarm time.")

    def toggle_alarm(self):
        if self.alarm_status.get() == "Off":
            self.alarm_status.set("On")
            self.alarm_switch.config(fg="green")
        else:
            self.alarm_status.set("Off")
            self.alarm_switch.config(fg="red")

    def check_alarm(self):
        while True:
            if self.alarm_status.get() == "On":
                current_time = datetime.datetime.now().strftime("%H:%M")
                if current_time == self.alarm_time.get():
                    self.show_alarm_message()
                    self.alarm_status.set("Off")
                    self.alarm_switch.config(fg="red")
            time.sleep(1)

    def show_alarm_message(self):
        messagebox.showinfo("⏰ Alarm!", "Time to Wake up/Drink!")

    def show_water_graph(self):
        import matplotlib.pyplot as plt
        plt.plot(self.water_intake)
        plt.title("Water Intake Over Time")
        plt.xlabel("Days")
        plt.ylabel("Water Intake (ml)")
        plt.show()

    def show_sleep_graph(self):
        import matplotlib.pyplot as plt
        plt.plot(self.sleep_time)
        plt.title("Sleep Hours Over Time")
        plt.xlabel("Days")
        plt.ylabel("Sleep Duration (hours)")
        plt.show()

    def show_water_info(self):
        messagebox.showinfo("Why is Drinking Water Important?",
                            "Drinking water is essential for hydration, digestion, and maintaining healthy skin.")

    def show_sleep_info(self):
        messagebox.showinfo("Why is Sleeping Important?",
                            "Sleep is crucial for physical and mental health, helping with memory consolidation and tissue repair.")

if __name__ == "__main__":
    root = tk.Tk()
    app = AlarmApp(root)
    root.mainloop()

import tkinter as tk
from tkinter import ttk
from tkinter import messagebox
from matplotlib.figure import Figure
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg
from datetime import datetime
import psutil
from plyer import notification

# Constants
UPDATE_INTERVAL = 1000  # milliseconds
CPU_THRESHOLD = 80  # Example threshold for CPU usage
MEMORY_THRESHOLD = 80  # Example threshold for memory usage
DISK_THRESHOLD = 80  # Example threshold for disk usage

class ResourceMonitorApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Resource Monitor")

        # Variables for storing resource usage data
        self.cpu_usage_data = []
        self.memory_usage_data = []
        self.disk_usage_data = []

        # Create GUI components
        self.create_gui()

        # Set up periodic data collection
        self.root.after(UPDATE_INTERVAL, self.update_data)

    def create_gui(self):
        # Create frames
        frame_left = ttk.Frame(self.root)
        frame_left.pack(side=tk.LEFT, padx=10, pady=10)

        frame_right = ttk.Frame(self.root)
        frame_right.pack(side=tk.LEFT, padx=10, pady=10)

        # Create real-time plots
        self.create_plots(frame_left)

        # Create alert thresholds
        self.create_thresholds(frame_right)

    def create_plots(self, frame):
        fig = Figure(figsize=(5, 4), dpi=100)
        self.ax_cpu = fig.add_subplot(311)
        self.ax_memory = fig.add_subplot(312)
        self.ax_disk = fig.add_subplot(313)

        self.canvas = FigureCanvasTkAgg(fig, master=frame)
        self.canvas.get_tk_widget().pack(side=tk.TOP, fill=tk.BOTH, expand=1)
        self.canvas.draw()

    def create_thresholds(self, frame):
        ttk.Label(frame, text="Alert Thresholds").pack()

        ttk.Label(frame, text="CPU Usage (%)").pack()
        self.cpu_threshold_entry = ttk.Entry(frame)
        self.cpu_threshold_entry.pack()

        ttk.Label(frame, text="Memory Usage (%)").pack()
        self.memory_threshold_entry = ttk.Entry(frame)
        self.memory_threshold_entry.pack()

        ttk.Label(frame, text="Disk Usage (%)").pack()
        self.disk_threshold_entry = ttk.Entry(frame)
        self.disk_threshold_entry.pack()

        ttk.Button(frame, text="Apply Thresholds", command=self.apply_thresholds).pack()

    def apply_thresholds(self):
        try:
            global CPU_THRESHOLD, MEMORY_THRESHOLD, DISK_THRESHOLD
            CPU_THRESHOLD = int(self.cpu_threshold_entry.get())
            MEMORY_THRESHOLD = int(self.memory_threshold_entry.get())
            DISK_THRESHOLD = int(self.disk_threshold_entry.get())
            messagebox.showinfo("Thresholds Applied", "Alert thresholds updated successfully.")
        except ValueError:
            messagebox.showerror("Invalid Input", "Please enter valid numerical values for thresholds.")

    def collect_data(self):
        cpu_percent = psutil.cpu_percent()
        memory_percent = psutil.virtual_memory().percent
        disk_percent = psutil.disk_usage('/').percent

        self.cpu_usage_data.append(cpu_percent)
        self.memory_usage_data.append(memory_percent)
        self.disk_usage_data.append(disk_percent)

        # Check if thresholds are exceeded and send alerts
        if cpu_percent > CPU_THRESHOLD:
            self.send_alert('High CPU Usage', f'CPU Usage: {cpu_percent}%')

        if memory_percent > MEMORY_THRESHOLD:
            self.send_alert('High Memory Usage', f'Memory Usage: {memory_percent}%')

        if disk_percent > DISK_THRESHOLD:
            self.send_alert('High Disk Usage', f'Disk Usage: {disk_percent}%')

    def send_alert(self, title, message):
        notification.notify(
            title=title,
            message=message,
            app_name='Resource Monitor',
        )

    def update_plot(self):
        self.ax_cpu.clear()
        self.ax_memory.clear()
        self.ax_disk.clear()

        self.ax_cpu.plot(self.cpu_usage_data, label='CPU Usage')
        self.ax_memory.plot(self.memory_usage_data, label='Memory Usage')
        self.ax_disk.plot(self.disk_usage_data, label='Disk Usage')

        self.ax_cpu.set_title('CPU Usage')
        self.ax_memory.set_title('Memory Usage')
        self.ax_disk.set_title('Disk Usage')

        self.canvas.draw()

    def update_data(self):
        self.collect_data()
        self.update_plot()

        # Schedule the next update
        self.root.after(UPDATE_INTERVAL, self.update_data)

if __name__ == "__main__":
    root = tk.Tk()
    app = ResourceMonitorApp(root)
    root.mainloop()

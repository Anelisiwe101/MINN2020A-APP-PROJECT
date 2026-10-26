# MINN2020A-APP-PROJECT
# KHWEZI MINING - HEALTH, SAFETY & EQUIPMENT MONITORING APPLICATION
# MINN2020A Computer Programming for Mining - 2026 Project

import csv
import os
import tkinter as tk
from tkinter import messagebox, ttk

import customtkinter as ctk
import pandas as pd
import matplotlib.pyplot as plt
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg

# APPLICATION SETTINGS & THEME
ctk.set_appearance_mode("dark")
ctk.set_default_color_theme("dark-blue")

APP_WIDTH = 1250
APP_HEIGHT = 780

USERS_FILE = "khwezi_users.csv"
WORKERS_FILE = "workers.csv"
INCIDENTS_FILE = "incidents.csv"
EQUIPMENT_FILE = "equipment.csv"

# -----------------------------
# SAMPLE DATA
# -----------------------------
def create_sample_csv_files():
    """Create prototype CSV data if the files do not exist."""

    if not os.path.exists(WORKERS_FILE):
        workers = pd.DataFrame([
            ["W001", "Mining", "Driller", "Day", 94, "Complete", 2, 3,
1, 0],
            ["W002", "Mining", "Haul Truck Operator", "Night", 78,
"Complete", 4, 2, 2, 1],
            ["W003", "Processing", "Plant Operator", "Day", 89,
"Complete", 2, 1, 0, 0],
            ["W004", "Maintenance", "Fitter & Turner", "Night", 72,
"Incomplete", 5, 4, 2, 3],
            ["W005", "Mining", "Blaster", "Day", 97, "Complete", 1, 0,
0, 0],
            ["W006", "Engineering", "Electrical Technician", "Day",
99, "Complete", 1, 1, 0, 0],
            ["W007", "Processing", "Mill Operator", "Night", 74,
"Incomplete", 4, 3, 2, 2],
            ["W008", "Mining", "Loader Operator", "Day", 91,
"Complete", 2, 1, 1, 1],
            ["W009", "Maintenance", "Auto Electrician", "Night", 83,
"Complete", 3, 2, 1, 1],
            ["W010", "Processing", "Control Room Operator", "Day", 96,
"Complete", 1, 0, 0, 0],
            ["W011", "Mining", "Shotcrete Operator", "Night", 76,
"Incomplete", 4, 3, 2, 2],
            ["W012", "Engineering", "Boilermaker", "Day", 92,
"Complete", 2, 1, 0, 1],
            ["W013", "Mining", "Face Supervisor", "Day", 98,
"Complete", 1, 0, 0, 0],
            ["W014", "Processing", "Crusher Operator", "Night", 70,
"Incomplete", 5, 4, 3, 4],
            ["W015", "Maintenance", "Rigger", "Day", 88, "Complete",
3, 1, 1, 1],
        ], columns=[
            "WorkerID", "Department", "JobRole", "Shift",
            "PPECompliance", "TrainingStatus", "FatigueLevel",
            "SafetyObservations", "NearMisses", "PreviousIncidents"
        ])
        workers.to_csv(WORKERS_FILE, index=False)

    if not os.path.exists(INCIDENTS_FILE):
        incidents = pd.DataFrame([
            ["I001", "2026-08-01", "07:20", "Day", "Pit A - Bench 4",
"Mining", "Vehicle", "Medium", "No", "No", "Haul truck reversed into
berm; spotter not in position", "Refresher training on reversing
procedures", "Closed"],
            ["I002", "2026-08-02", "19:40", "Night", "Plant 1 -
Conveyor CV-01", "Processing", "Slip/Trip", "Low", "No", "No", "Water
spillage on walkway from pump gland leak", "Repair gland; install drip
tray; housekeeping schedule", "Closed"],
            ["I003", "2026-08-04", "10:10", "Day", "Pit B - Ramp 2",
"Mining", "Equipment", "High", "No", "Yes", "Excavator EXC-001 brake
accumulator failure", "Remove from service; OEM inspection; brake
system audit", "Open"],
            ["I004", "2026-08-06", "21:15", "Night", "Workshop - Bay
3", "Maintenance", "Electrical", "High", "Yes", "Yes", "Arc flash
during panel work; LOTO not applied", "Isolate and lock out; review
LOTO procedure; retrain", "Open"],
            ["I005", "2026-08-08", "13:30", "Day", "Plant 2 - Reagent
Store", "Processing", "Chemical", "Medium", "No", "No", "Sodium
cyanide drum seal failure; small spill contained", "Bund integrity
check; PPE upgrade; spill drill", "Closed"],
            ["I006", "2026-08-10", "20:00", "Night", "Pit A -
Intersection 3", "Mining", "Vehicle", "Critical", "Yes", "Yes", "LDV
and haul truck collision at uncontrolled intersection", "Full
investigation; traffic management review; stop-work", "Open"],
            ["I007", "2026-08-13", "09:25", "Day", "Pit C - Highwall",
"Mining", "Fall", "Medium", "No", "No", "Rockfall from unsupported
highwall section", "Geotech inspection; scaling; support
installation", "Closed"],
            ["I008", "2026-08-15", "18:10", "Night", "Plant 1 - Mill
Drive", "Processing", "Equipment", "High", "No", "Yes", "Mill gearbox
oil temperature high; auto-shutdown", "Oil analysis; cooler cleaning;
vibration survey", "Open"],
            ["I009", "2026-08-18", "11:45", "Day", "Workshop -
Fabrication", "Maintenance", "Hand injury", "Low", "Yes", "No", "Angle
grinder kickback; guard removed", "Guard replacement; tool safety
training; PPE audit", "Closed"],
            ["I010", "2026-08-20", "22:10", "Night", "Pit B - Haul
Road", "Mining", "Vehicle", "High", "No", "Yes", "Haul truck operator
fatigue; lane departure", "Fatigue management review; rest break
schedule", "Open"],
            ["I011", "2026-08-22", "14:30", "Day", "Processing -
Thickener", "Processing", "Fall", "Medium", "No", "No", "Slip on
walkway grating; handrail missing section", "Replace grating; install
handrail; inspection", "Closed"],
            ["I012", "2026-08-25", "08:45", "Day", "Engineering -
Substation", "Engineering", "Electrical", "High", "No", "Yes", "Cable
termination overheating; smoke detected", "Thermal scan; re-terminate;
protection review", "Open"],
        ], columns=[
            "IncidentID", "Date", "Time", "Shift", "Location",
"Department",
            "IncidentType", "Severity", "InjuryStatus", "LostTime",
            "Cause", "CorrectiveAction", "Status"
        ])
        incidents.to_csv(INCIDENTS_FILE, index=False)

    if not os.path.exists(EQUIPMENT_FILE):
        equipment = pd.DataFrame([
            ["TRK-001", "Haul Truck", "CAT 793F", 18400, 78, 3.8, 68,
"Good", "Good", "Good", "Up to date", 12, 94, "2026-09-30"],
            ["TRK-002", "Haul Truck", "Komatsu HD785", 22100, 91, 7.4,
82, "Good", "Warning", "Good", "Due", 38, 81, "2026-09-20"],
            ["TRK-003", "Haul Truck", "CAT 777E", 15600, 74, 4.1, 61,
"Good", "Good", "Good", "Up to date", 8, 96, "2026-10-05"],
            ["TRK-004", "Haul Truck", "Komatsu HD465", 27300, 103,
9.8, 95, "Warning", "Warning", "Warning", "Overdue", 72, 68, "2026-08-
28"],
            ["EXC-001", "Excavator", "Hitachi EX2600", 19800, 106,
8.9, 102, "Warning", "Good", "Warning", "Overdue", 68, 74, "2026-08-
25"],
            ["EXC-002", "Excavator", "Komatsu PC5500", 12400, 82, 5.2,
78, "Good", "Good", "Good", "Due", 28, 87, "2026-09-18"],
            ["DRL-001", "Drilling Machine", "Sandvik DR410i", 8600,
76, 3.8, 64, "Good", "Good", "Good", "Up to date", 10, 94, "2026-10-
15"],
            ["DRL-002", "Drilling Machine", "Epiroc Pit Viper 271",
14300, 88, 6.1, 71, "Good", "Warning", "Good", "Due", 35, 85, "2026-
09-12"],
            ["LD-001", "Loader", "CAT 992K", 11200, 84, 5.5, 72,
"Good", "Warning", "Good", "Due", 31, 84, "2026-09-25"],
            ["LD-002", "Loader", "Komatsu WA800", 16800, 97, 6.8, 88,
"Warning", "Good", "Good", "Due", 44, 79, "2026-09-10"],
            ["CR-001", "Crusher", "Metso C160 Jaw", 24500, 112, 10.4,
118, "Critical", "Good", "Warning", "Overdue", 96, 64, "2026-08-15"],
            ["CR-002", "Crusher", "Sandvik CH660 Cone", 18900, 98,
7.9, 95, "Warning", "Warning", "Good", "Due", 52, 76, "2026-09-05"],
            ["CV-001", "Conveyor", "FLSmidth CV-01", 13200, 69, 2.9,
52, "Good", "Good", "Good", "Up to date", 8, 96, "2026-11-01"],
            ["CV-002", "Conveyor", "FLSmidth CV-02", 14700, 72, 3.4,
55, "Good", "Good", "Good", "Up to date", 11, 95, "2026-10-20"],
            ["BLD-001", "Bulldozer", "Komatsu D375A", 9800, 82, 4.7,
70, "Good", "Good", "Good", "Up to date", 18, 91, "2026-10-10"],
            ["GRD-001", "Grader", "CAT 16M", 7600, 74, 3.2, 48,
"Good", "Good", "Good", "Up to date", 9, 95, "2026-10-25"],
            ["WSH-001", "Water Cart", "CAT 773F WT", 10500, 79, 4.4,
58, "Good", "Good", "Good", "Due", 22, 89, "2026-09-28"],
            ["GEN-001", "Generator", "Cummins C1500 D5", 28600, 94,
2.1, 145, "Good", "N/A", "Good", "Due", 41, 88, "2026-09-15"],
        ], columns=[
            "EquipmentID", "EquipmentType", "Manufacturer",
"OperatingHours",
            "Temperature", "Vibration", "FuelConsumption",
"BrakeStatus",
            "TyreStatus", "EngineStatus", "MaintenanceStatus",
"Downtime",
            "Availability", "MaintenanceDue"
        ])
        equipment.to_csv(EQUIPMENT_FILE, index=False)

# MAIN APPLICATION

class KhweziMiningApp:
    def __init__(self, root):
        self.root = root
        self.root.geometry(f"{APP_WIDTH}x{APP_HEIGHT}")
        self.root.minsize(1100, 700)
        self.root.title("Khwezi Mining | Health, Safety & Equipment
Monitoring")

        self.current_user = None
        self.current_role = None

        # NEW COLOR PALETTE: Deep Forest & Copper
        self.colors = {
              "bg": "#0F1715",           # Deep dark green/black
              "panel": "#1A2420",        # Slightly lighter dark green
              "panel2": "#24332D",       # Card background
              "accent": "#D98A44",       # Copper/Orange
              "accent_hover": "#E89E5F",
              "accent2": "#3B8EA5",      # Teal/Blue
              "success": "#48A97C",      # Soft green
              "warning": "#D98A44",      # Copper
              "danger": "#C75B5B",       # Soft red
              "text": "#F2F5F4",         # Off-white
              "muted": "#8FA39C",        # Greyish-green
              "border": "#2E4239"        # Subtle border
          }

          create_sample_csv_files()
          self.load_data()
          self.load_users()
          self.show_login()

             username = self.username_entry.get().strip()
           password = self.password_entry.get().strip()
        if username in self.users and self.users[username]["password"]
== password:
             self.current_user = username
             self.current_role = self.users[username]["role"]
             self.dashboard()
        else:
            messagebox.showerror("Login Failed", "Invalid username or
password.")

    def registration(self):
        self.clear()
        self.header("Create Account", "Register a user for the
monitoring application.")
        form = ctk.CTkFrame(self.root, fg_color=self.colors["panel"],
corner_radius=15)
        form.pack(pady=30, padx=30)

        ctk.CTkLabel(form, text="Username").pack(pady=(20, 5))
        username = ctk.CTkEntry(form, width=300,
fg_color=self.colors["bg"], border_color=self.colors["border"])
        username.pack(pady=5, padx=40)

        ctk.CTkLabel(form, text="Password").pack(pady=(10, 5))
        password = ctk.CTkEntry(form, width=300, show="*",
fg_color=self.colors["bg"], border_color=self.colors["border"])
        password.pack(pady=5, padx=40)

        ctk.CTkLabel(form, text="Role").pack(pady=(10, 5))
        role = ctk.StringVar(value="Safety Officer")
        role_box = ctk.CTkComboBox(form, values=["Administrator",
"Safety Officer", "Mining Engineer",
                                                 "Maintenance
Engineer", "Manager"],
                                   variable=role, width=300,
fg_color=self.colors["bg"], border_color=self.colors["border"])
        role_box.pack(pady=5, padx=40)

        def register():
            u = username.get().strip()
            p = password.get().strip()
            if not u or not p:
                messagebox.showerror("Error", "Username and password
are required.")
                return
            if u in self.users:
                messagebox.showerror("Error", "Username already
exists.")
                return
            self.users[u] = {"password": p, "role": role.get()}
            self.save_users()
            messagebox.showinfo("Success", "Account created. You can
now log in.")
            self.show_login()

        ctk.CTkButton(form, text="Register", width=300,
fg_color=self.colors["accent"],
hover_color=self.colors["accent_hover"],
command=register).pack(pady=20)
        ctk.CTkButton(form, text="Back to Login", width=300,
fg_color="transparent", border_width=1,
border_color=self.colors["border"],
command=self.show_login).pack(pady=(0, 20))

    # -------------------------        

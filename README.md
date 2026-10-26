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
# DASHBOARD (Fits on One Page)
    # -------------------------
    def dashboard(self):
        self.clear()
        permissions = self.role_permissions()

        # Top Bar
        top_bar = ctk.CTkFrame(self.root,
fg_color=self.colors["panel"], corner_radius=0, height=80)
        top_bar.pack(fill="x")
        top_bar.pack_propagate(False)

        ctk.CTkLabel(top_bar, text="⛏ Khwezi Mining Dashboard",
font=("Arial", 24, "bold"),

text_color=self.colors["accent"]).pack(side="left", padx=25)

       # User info and Logout in top bar
       user_frame = ctk.CTkFrame(top_bar, fg_color="transparent")
       user_frame.pack(side="right", padx=25)

        ctk.CTkButton(user_frame, text="LOG OUT", width=90, height=32,
corner_radius=6,
                      fg_color=self.colors["danger"],
hover_color="#A84C4C",
                      command=self.logout).pack(side="right",
padx=(15, 0))

        ctk.CTkLabel(user_frame, text=f"{self.current_user}
({self.current_role})",
                     font=("Arial", 13),
text_color=self.colors["muted"]).pack(side="right")

       # KPI Cards (Single Row)
       kpi_frame = ctk.CTkFrame(self.root, fg_color="transparent")
       kpi_frame.pack(fill="x", padx=20, pady=(20, 10))
        critical_equipment = len(self.equipment_df[
            (self.equipment_df["Temperature"] > 100) |
(self.equipment_df["Vibration"] > 8)])
        high_critical_incidents = len(self.incidents_df[
            self.incidents_df["Severity"].isin(["High", "Critical"])])
        near_misses = int(self.workers_df["NearMisses"].sum())
        avg_availability = self.equipment_df["Availability"].mean()
        ppe = self.workers_df["PPECompliance"].mean()

        cards = [
            ("WORKERS", len(self.workers_df), self.colors["accent2"]),
            ("INCIDENTS", len(self.incidents_df),
self.colors["danger"]),
            ("NEAR MISSES", near_misses, self.colors["accent"]),
            ("EQUIPMENT", len(self.equipment_df),
self.colors["accent2"]),
            ("AVAILABILITY", f"{avg_availability:.1f}%",
self.colors["success"]),
            ("PPE COMPLIANCE", f"{ppe:.1f}%", self.colors["success"]),
            ("HIGH/CRITICAL", high_critical_incidents,
self.colors["danger"]),
            ("EQ ALERTS", critical_equipment, self.colors["danger"])
        ]

        # 8 cards in a single row
        for i, (label, value, color) in enumerate(cards):
            card = ctk.CTkFrame(kpi_frame,
fg_color=self.colors["panel2"], corner_radius=10, border_width=1,
border_color=self.colors["border"])
            card.pack(side="left", fill="both", expand=True, padx=4)

           ctk.CTkLabel(card, text=label, font=("Arial", 9, "bold"),

text_color=self.colors["muted"]).pack(pady=(12, 0))
            ctk.CTkLabel(card, text=str(value), font=("Arial", 22,
"bold"),
                         text_color=color).pack(pady=(2, 12))

        # Navigation Menu (compact 4-row x 2-column grid)
        # Keep the menu centred and sized to its contents instead of
stretching
        # across the whole dashboard. Each button stays compact and
readable.
        nav_frame = ctk.CTkFrame(self.root, fg_color="transparent")
        nav_frame.pack(anchor="center", padx=20, pady=(10, 6))

        buttons = [
            ("👷 Worker Health & Safety", self.worker_monitoring,
permissions["workers"]),
            ("⚠️ Safety Incidents", self.incident_database,
permissions["incidents"]),
            ("🚜 Equipment Database", self.equipment_database,
permissions["equipment"]),
            ("🔧 Maintenance Monitoring", self.maintenance_monitoring,
permissions["maintenance"]),
            ("📊 Risk Assessment", self.risk_assessment,
permissions["risk"]),
            ("🔔 Alerts & Notifications", self.alerts, True),
            ("📈 Interactive Data Analysis", self.analytics, True),
            ("📋 Generate / Export Report", self.export_report,
permissions["reports"]),
            ("👥 User Management", self.user_management,
permissions["users"]),
        ]


        row, col = 0, 0
        for text, command, allowed in buttons:
            if allowed:
                btn = ctk.CTkButton(
                    nav_frame, text=text, command=command,
                    width=330, height=48, corner_radius=8,
                    fg_color=self.colors["panel2"],
                    border_width=1,
border_color=self.colors["border"],
                    hover_color=self.colors["border"],
                    text_color=self.colors["text"],
                    font=("Arial", 12, "bold"),
                    anchor="center"
                )
                btn.grid(row=row, column=col, padx=7, pady=5)

               col += 1
               if col >= 2:
                   col = 0
                   row += 1

        # Fixed column sizing prevents the buttons from expanding to
fill space.
        nav_frame.grid_columnconfigure(0, weight=0)
        nav_frame.grid_columnconfigure(1, weight=0)

        # Footer
        ctk.CTkLabel(self.root, text="Prototype educational thresholds
only — not actual mine safety limits.",
                     font=("Arial", 10),
text_color=self.colors["muted"]).pack(pady=5)

    # WORKER MONITORING
    def worker_monitoring(self):
        self.clear()
        self.header("Worker Health & Safety Monitoring",
                     "Worker PPE, training, fatigue, observations and
previous incident monitoring.")
        frame = ctk.CTkFrame(self.root, fg_color=self.colors["panel"],
corner_radius=12, border_width=1, border_color=self.colors["border"])
        frame.pack(fill="both", expand=True, padx=20, pady=10)
        self.make_tree(frame, list(self.workers_df.columns),
self.workers_df)

        warning_workers = self.workers_df[
            (self.workers_df["PPECompliance"] < 80) |
            (self.workers_df["FatigueLevel"] >= 4) |
            (self.workers_df["TrainingStatus"] != "Complete")]
        ctk.CTkLabel(self.root,
                     text=f"Warning: {len(warning_workers)} workers
require attention.",
                     text_color=self.colors["accent"],
                     font=("Arial", 13, "bold")).pack(pady=5)
        self.back_button()

    # INCIDENT DATABASE
    def incident_database(self):
        self.clear()
        self.header("Safety Incident Database",
                     "Add, search, filter, categorise, count and
analyse safety incidents.")

        toolbar = ctk.CTkFrame(self.root, fg_color="transparent")
        toolbar.pack(fill="x", padx=20, pady=(5, 2))

        search_var = tk.StringVar()
        severity = ctk.StringVar(value="All")
        incident_type = ctk.StringVar(value="All")
        department = ctk.StringVar(value="All")
        status = ctk.StringVar(value="All")

        ctk.CTkLabel(toolbar, text="Search:",
text_color=self.colors["text"]).pack(side="left", padx=(0, 5))
        search_entry = ctk.CTkEntry(toolbar, textvariable=search_var,
placeholder_text="ID, location, cause...", width=190,
                                     fg_color=self.colors["bg"],
border_color=self.colors["border"])
        search_entry.pack(side="left", padx=(0, 10))

        def add_filter(label, variable, values, width=115):
            ctk.CTkLabel(toolbar, text=label,
text_color=self.colors["text"]).pack(side="left", padx=(4, 3))
            combo = ctk.CTkComboBox(toolbar, values=values,
variable=variable, width=width,
                                      fg_color=self.colors["bg"],
border_color=self.colors["border"],
                                   command=lambda _: refresh())
           combo.pack(side="left", padx=(0, 4))
           return combo

        add_filter("Severity:", severity, ["All", "Low", "Medium",
"High", "Critical"], 100)
        add_filter("Type:", incident_type, ["All"] +
sorted(self.incidents_df["IncidentType"].dropna().astype(str).unique()
.tolist()), 115)
        add_filter("Department:", department, ["All"] +
sorted(self.incidents_df["Department"].dropna().astype(str).unique().t
olist()), 120)
        add_filter("Status:", status, ["All", "Open", "Closed"], 90)
        ctk.CTkButton(toolbar, text="Clear Filters", width=95,
                      fg_color=self.colors["panel2"],
hover_color=self.colors["border"],
                      command=lambda:
clear_filters()).pack(side="left", padx=(5, 0))

        action_bar = ctk.CTkFrame(self.root, fg_color="transparent")
        action_bar.pack(fill="x", padx=20, pady=(2, 5))
        count_label = ctk.CTkLabel(action_bar, text="",
text_color=self.colors["accent"],
                                   font=("Arial", 12, "bold"))
        count_label.pack(side="left")
        ctk.CTkButton(action_bar, text="+ Add Incident", width=130,
                      fg_color=self.colors["accent"],
hover_color=self.colors["accent_hover"],
                      command=lambda:
add_incident()).pack(side="right", padx=(5, 0))
        ctk.CTkButton(action_bar, text="Analyse Trends", width=130,
                      fg_color=self.colors["accent2"],
hover_color=self.colors["border"],
                      command=lambda:
analyse_trends()).pack(side="right", padx=5)

        frame = ctk.CTkFrame(self.root, fg_color=self.colors["panel"],
corner_radius=12,
                             border_width=1,
border_color=self.colors["border"])
        frame.pack(fill="both", expand=True, padx=20, pady=(3, 8))

       def refresh(*_):
           for widget in frame.winfo_children():
               widget.destroy()
           df = self.incidents_df.copy()
           if severity.get() != "All":
                df = df[df["Severity"].astype(str) == severity.get()]
            if incident_type.get() != "All":
                df = df[df["IncidentType"].astype(str) ==
incident_type.get()]
            if department.get() != "All":
                df = df[df["Department"].astype(str) ==
department.get()]
            if status.get() != "All":
                df = df[df["Status"].astype(str) == status.get()]
            query = search_var.get().strip().lower()
            if query:
                matches = df.astype(str).apply(lambda column:
column.str.lower().str.contains(query, na=False,
regex=False)).any(axis=1)
                df = df[matches]
            count_label.configure(text=f"Showing {len(df)} of
{len(self.incidents_df)} incidents | Total recorded:
{len(self.incidents_df)}")
            self.make_tree(frame, list(df.columns), df)

       def clear_filters():
           search_var.set("")
           severity.set("All")
           incident_type.set("All")
           department.set("All")
           status.set("All")
           refresh()

       search_var.trace_add("write", refresh)

        def add_incident():
            popup = ctk.CTkToplevel(self.root)
            popup.title("Add Safety Incident")
            popup.geometry("650x700")
            popup.transient(self.root)
            popup.grab_set()
            ctk.CTkLabel(popup, text="Add Safety Incident",
font=("Arial", 20, "bold"),

text_color=self.colors["accent"]).pack(pady=(15, 8))
            form = ctk.CTkScrollableFrame(popup,
fg_color=self.colors["panel"])
            form.pack(fill="both", expand=True, padx=18, pady=8)

            fields = [
                ("Date (YYYY-MM-DD)", "Date", "2026-10-20"), ("Time
(HH:MM)", "Time", ""),
                ("Shift", "Shift", "Day"), ("Location", "Location",
""),
                ("Department", "Department", "Mining"), ("Incident
Type", "IncidentType", "Vehicle"),
                ("Severity", "Severity", "Medium"), ("Injury Status",
"InjuryStatus", "No"),
                ("Lost Time", "LostTime", "No"), ("Cause /
Description", "Cause", ""),
                ("Corrective Action", "CorrectiveAction", ""),
("Status", "Status", "Open")
            ]
            entries = {}
            for label, key, default in fields:
                ctk.CTkLabel(form, text=label,
text_color=self.colors["text"]).pack(anchor="w", padx=10, pady=(5, 0))
                if key == "Shift":
                    widget = ctk.CTkComboBox(form, values=["Day",
"Night"], width=560)
                    widget.set(default)
                elif key == "Department":
                    widget = ctk.CTkComboBox(form, values=["Mining",
"Processing", "Maintenance", "Engineering", "Other"], width=560)
                    widget.set(default)
                elif key == "IncidentType":
                    widget = ctk.CTkComboBox(form, values=["Vehicle",
"Slip/Trip", "Equipment", "Electrical", "Chemical", "Fall", "Hand
injury", "Fire", "Ground Control", "Other"], width=560)
                    widget.set(default)
                elif key == "Severity":
                    widget = ctk.CTkComboBox(form, values=["Low",
"Medium", "High", "Critical"], width=560)
                    widget.set(default)
                elif key in ("InjuryStatus", "LostTime"):
                    widget = ctk.CTkComboBox(form, values=["No",
"Yes"], width=560)
                    widget.set(default)
                elif key == "Status":
                    widget = ctk.CTkComboBox(form, values=["Open",
"Closed"], width=560)
                    widget.set(default)
                else:
                    widget = ctk.CTkEntry(form, width=560,
placeholder_text=label)
                    if default:
                        widget.insert(0, default)
                widget.pack(padx=10, pady=(2, 4))
                entries[key] = widget

            def save_incident():
                 values = {key: widget.get().strip() for key, widget in
entries.items()}
                 required = ["Date", "Time", "Location", "Department",
"IncidentType", "Severity", "Cause"]
                if any(not values[key] for key in required):
                    messagebox.showerror("Missing Information",
"Please complete the date, time, location, department, incident type,
severity and cause.", parent=popup)
                    return
                try:
                    pd.to_datetime(values["Date"], format="%Y-%m-%d",
errors="raise")
                    pd.to_datetime(values["Time"], format="%H:%M",
errors="raise")
                except ValueError:
                    messagebox.showerror("Invalid Date or Time", "Use
YYYY-MM-DD for the date and HH:MM for the time.", parent=popup)
                    return
                next_number = 1
                existing_ids =
self.incidents_df["IncidentID"].astype(str).tolist()
                while f"I{next_number:03d}" in existing_ids:
                    next_number += 1
                record = {"IncidentID": f"I{next_number:03d}"}
                record.update(values)
                try:
                    updated = pd.concat([self.incidents_df,
pd.DataFrame([record], columns=self.incidents_df.columns)],
ignore_index=True)
                    updated.to_csv(INCIDENTS_FILE, index=False)
                    self.incidents_df = updated
                    popup.destroy()
                    refresh()
                    messagebox.showinfo("Incident Added", f"Incident
{record['IncidentID']} was saved successfully.", parent=self.root)
                except Exception as exc:
                    messagebox.showerror("Save Error", f"The incident
could not be saved: {exc}", parent=popup)

            ctk.CTkButton(popup, text="Save Incident",
command=save_incident,
                          fg_color=self.colors["accent"],
hover_color=self.colors["accent_hover"],
                          width=200, height=38).pack(pady=(5, 15))

        def analyse_trends():
            if self.incidents_df.empty:
                messagebox.showinfo("Incident Trends", "There are no
incidents to analyse.")
                return
            df = self.incidents_df.copy()
            df["Date"] = pd.to_datetime(df["Date"], errors="coerce")
            df = df.dropna(subset=["Date"])
            if df.empty:
                messagebox.showinfo("Incident Trends", "No valid
incident dates are available for trend analysis.")
                return
            df["Month"] = df["Date"].dt.to_period("M").astype(str)
            monthly = df.groupby("Month").size()
            by_type = self.incidents_df["IncidentType"].value_counts()
            by_severity =
self.incidents_df["Severity"].value_counts().reindex(["Low", "Medium",
"High", "Critical"], fill_value=0)

            popup = ctk.CTkToplevel(self.root)
            popup.title("Safety Incident Trend Analysis")
            popup.geometry("950x650")
            popup.transient(self.root)
            fig, axes = plt.subplots(1, 3, figsize=(12, 4.5),
facecolor=self.colors["panel"])
            for ax in axes:
                ax.set_facecolor(self.colors["panel"])
                ax.tick_params(axis="x", rotation=35,
colors=self.colors["text"])
                ax.tick_params(axis="y", colors=self.colors["text"])
                ax.title.set_color(self.colors["text"])
            monthly.plot(kind="bar", ax=axes[0],
color=self.colors["accent"])
            axes[0].set_title("Incidents by Month")
            axes[0].set_ylabel("Number of incidents",
color=self.colors["text"])
            by_type.plot(kind="bar", ax=axes[1],
color=self.colors["accent2"])
            axes[1].set_title("Incidents by Category")
            axes[1].set_ylabel("Number of incidents",
color=self.colors["text"])
            by_severity.plot(kind="bar", ax=axes[2],
color=self.colors["danger"])
            axes[2].set_title("Incidents by Severity")
            axes[2].set_ylabel("Number of incidents",
color=self.colors["text"])
            fig.tight_layout()
            canvas = FigureCanvasTkAgg(fig, master=popup)
            canvas.draw()
            canvas.get_tk_widget().pack(fill="both", expand=True,
padx=10, pady=10)
            ctk.CTkLabel(popup, text=f"Total incidents:
{len(self.incidents_df)} | Open: {(self.incidents_df['Status'] ==
'Open').sum()} | High/Critical:
{self.incidents_df['Severity'].isin(['High', 'Critical']).sum()}",
                          text_color=self.colors["accent"],
font=("Arial", 13, "bold")).pack(pady=(0, 10))

        refresh()
        self.back_button()
 # EQUIPMENT DATABASE
    def equipment_database(self):
        self.clear()
        self.header("Equipment Database",
                     "Central equipment repository with operating
condition parameters.")
        frame = ctk.CTkFrame(self.root, fg_color=self.colors["panel"],
corner_radius=12, border_width=1, border_color=self.colors["border"])
        frame.pack(fill="both", expand=True, padx=20, pady=10)
        display_df = self.equipment_df.copy()
        display_df["Condition"] = display_df.apply(
            lambda r: self.condition_status(r["Temperature"],
r["Vibration"]), axis=1)
        self.make_tree(frame, list(display_df.columns), display_df)
        self.back_button()

    def condition_status(self, temperature, vibration):
        if temperature > 100 or vibration > 8:
            return "CRITICAL"
        if temperature >= 80 or vibration >= 5:
            return "WARNING"
        return "NORMAL"

    # MAINTENANCE
    def maintenance_monitoring(self):
        self.clear()
        self.header("Equipment Maintenance Monitoring",
                     "Maintenance due/overdue status, downtime and
availability.")
        df = self.equipment_df.copy()

        def maintenance_flag(row):
            if str(row["MaintenanceStatus"]).lower() == "overdue":
                return "OVERDUE"
            if row["Availability"] < 80:
                return "LOW AVAILABILITY"
            if row["Downtime"] > 50:
                return "EXCESSIVE DOWNTIME"
            if row["MaintenanceStatus"].lower() == "due":
                return "DUE"
            return "OK"

        df["Maintenance Alert"] = df.apply(maintenance_flag, axis=1)
        frame = ctk.CTkFrame(self.root, fg_color=self.colors["panel"],
corner_radius=12, border_width=1, border_color=self.colors["border"])
       frame.pack(fill="both", expand=True, padx=20, pady=10)
       self.make_tree(frame, list(df.columns), df)

        due = len(df[df["Maintenance Alert"].isin(
            ["OVERDUE", "DUE", "LOW AVAILABILITY", "EXCESSIVE
DOWNTIME"])])
        ctk.CTkLabel(self.root,
                     text=f"{due} equipment units require maintenance
attention.",
                     text_color=self.colors["accent"],
                     font=("Arial", 13, "bold")).pack(pady=5)
        self.back_button()


    # RISK ASSESSMENT
    def risk_assessment(self):
        self.clear()
        self.header("Risk Assessment",
                     "RiskScore = Likelihood × Consequence using the
required 1–5 scale.")
        form = ctk.CTkFrame(self.root, fg_color=self.colors["panel"],
corner_radius=15, border_width=1, border_color=self.colors["border"])
        form.pack(pady=30)

        ctk.CTkLabel(form, text="Likelihood (1–5)",
text_color=self.colors["text"]).grid(row=0, column=0, padx=20,
pady=15)
        likelihood = ctk.CTkComboBox(form, values=["1", "2", "3", "4",
"5"], width=100, fg_color=self.colors["bg"],
border_color=self.colors["border"])
        likelihood.set("3")
        likelihood.grid(row=0, column=1, padx=20)

        ctk.CTkLabel(form, text="Consequence (1–5)",
text_color=self.colors["text"]).grid(row=1, column=0, padx=20,
pady=15)
        consequence = ctk.CTkComboBox(form, values=["1", "2", "3",
"4", "5"], width=100, fg_color=self.colors["bg"],
border_color=self.colors["border"])
        consequence.set("3")
        consequence.grid(row=1, column=1, padx=20)

        result = ctk.CTkLabel(form, text="Risk score: 9 |
Classification: MEDIUM",
                              font=("Arial", 20, "bold"),
text_color=self.colors["accent"])
        result.grid(row=3, column=0, columnspan=2, pady=20)

       def calculate():
           score = int(likelihood.get()) * int(consequence.get())
            if score <= 4:
                classification, color = "LOW", self.colors["success"]
            elif score <= 9:
                classification, color = "MEDIUM",
self.colors["accent"]
            elif score <= 16:
                classification, color = "HIGH", "#E67E22"
            else:
                classification, color = "CRITICAL",
self.colors["danger"]
            result.configure(
                text=f"Risk score: {score} | Classification:
{classification}",
                text_color=color)

        ctk.CTkButton(form, text="Calculate Risk", command=calculate,
width=250, height=40,
                      fg_color=self.colors["accent"],
hover_color=self.colors["accent_hover"]).grid(row=2, column=0,
columnspan=2, pady=10)

        self.back_button()

    # ALERTS
    def alerts(self):
        self.clear()
        self.header("Alerts & Notifications",
                     "Automatically generated from predefined prototype
conditions.")
        alerts = []

        for _, r in self.equipment_df.iterrows():
            status = self.condition_status(r["Temperature"],
r["Vibration"])
            if status == "CRITICAL":
                if r["Temperature"] > 100:
                    alerts.append(("CRITICAL", r["EquipmentID"],
                                   f"Temperature {r['Temperature']}°C
exceeds critical threshold."))
                if r["Vibration"] > 8:
                    alerts.append(("CRITICAL", r["EquipmentID"],
                                   f"Vibration {r['Vibration']} mm/s
exceeds critical threshold."))
            elif status == "WARNING":
                alerts.append(("WARNING", r["EquipmentID"],
                               f"Equipment approaching monitoring
threshold "
                               f"(Temp {r['Temperature']}°C, vibration
{r['Vibration']} mm/s)."))
        for _, r in self.workers_df.iterrows():
            if r["PPECompliance"] < 80:
                alerts.append(("WARNING", r["WorkerID"],
                               f"PPE compliance is
{r['PPECompliance']}%."))
            if r["FatigueLevel"] >= 4:
                alerts.append(("WARNING", r["WorkerID"],
                               f"Fatigue level is
{r['FatigueLevel']}/5."))

         for _, r in self.incidents_df.iterrows():
             if r["Severity"] in ["High", "Critical"] and r["Status"]
== "Open":
                 alerts.append(("CRITICAL", r["IncidentID"],
                                f"{r['Severity']} incident remains
open."))

        scroll = ctk.CTkScrollableFrame(self.root,
fg_color=self.colors["panel"], corner_radius=12, border_width=1,
border_color=self.colors["border"])
        scroll.pack(fill="both", expand=True, padx=20, pady=10)

        if not alerts:
            ctk.CTkLabel(scroll, text="No active alerts.",
                         font=("Arial", 18, "bold"),

text_color=self.colors["success"]).pack(pady=40)
        else:
            for level, identifier, text in alerts:
                color = self.colors["danger"] if level == "CRITICAL"
else self.colors["accent"]
                card = ctk.CTkFrame(scroll,
fg_color=self.colors["panel2"], corner_radius=8)
                card.pack(fill="x", padx=10, pady=4)
                ctk.CTkLabel(card, text=f"[{level}] {identifier}",
                             font=("Arial", 13, "bold"),
                             text_color=color).pack(anchor="w",
padx=15, pady=(10, 2))
                ctk.CTkLabel(card, text=text,
                             font=("Arial", 11),
text_color=self.colors["text"]).pack(anchor="w", padx=15, pady=(0,
10))

        self.back_button()

    # ANALYTICS
    def analytics(self):
        self.clear()
        self.header("Interactive Data Analysis",
                     "Business questions from the project brief
answered using the monitoring dataset.")
        tabs = ctk.CTkTabview(self.root,
fg_color=self.colors["panel"], border_width=1,
border_color=self.colors["border"])
        tabs.pack(fill="both", expand=True, padx=20, pady=10)
        tabs.add("Safety Analysis")
        tabs.add("Equipment Analysis")
        tabs.add("Integrated Analysis")

        safety_tab = tabs.tab("Safety Analysis")
        fig1, ax1 = plt.subplots(figsize=(7.5, 4),
facecolor=self.colors["panel"])
        ax1.set_facecolor(self.colors["panel"])

self.incidents_df["Department"].value_counts().plot(kind="bar",
ax=ax1, color=self.colors["accent"])
        ax1.set_title("Safety Incidents by Department",
color=self.colors["text"])
        ax1.set_ylabel("Number of incidents",
color=self.colors["text"])
        ax1.tick_params(axis="x", rotation=30,
colors=self.colors["text"])
        ax1.tick_params(axis="y", colors=self.colors["text"])
        fig1.tight_layout()
        canvas1 = FigureCanvasTkAgg(fig1, master=safety_tab)
        canvas1.draw()
        canvas1.get_tk_widget().pack(fill="both", expand=True)

        equipment_tab = tabs.tab("Equipment Analysis")
        fig2, ax2 = plt.subplots(figsize=(7.5, 4),
facecolor=self.colors["panel"])
        ax2.set_facecolor(self.colors["panel"])
        self.equipment_df.plot(x="EquipmentID", y="Availability",
kind="bar", ax=ax2, legend=False, color=self.colors["accent2"])
        ax2.set_title("Equipment Availability",
color=self.colors["text"])
        ax2.set_ylabel("Availability (%)", color=self.colors["text"])
        ax2.tick_params(axis="x", rotation=30,
colors=self.colors["text"])
        ax2.tick_params(axis="y", colors=self.colors["text"])
        fig2.tight_layout()
        canvas2 = FigureCanvasTkAgg(fig2, master=equipment_tab)
        canvas2.draw()
        canvas2.get_tk_widget().pack(fill="both", expand=True)

        integrated = tabs.tab("Integrated Analysis")
        fig3, ax3 = plt.subplots(figsize=(7.5, 4),
facecolor=self.colors["panel"])
        ax3.set_facecolor(self.colors["panel"])
        ax3.scatter(self.equipment_df["Downtime"],
self.equipment_df["Availability"], color=self.colors["accent"])
        ax3.set_title("Downtime vs Equipment Availability",
color=self.colors["text"])
        ax3.set_xlabel("Downtime", color=self.colors["text"])
        ax3.set_ylabel("Availability (%)", color=self.colors["text"])
        ax3.tick_params(axis="x", colors=self.colors["text"])
        ax3.tick_params(axis="y", colors=self.colors["text"])
        fig3.tight_layout()
        canvas3 = FigureCanvasTkAgg(fig3, master=integrated)
        canvas3.draw()
        canvas3.get_tk_widget().pack(fill="both", expand=True)

        answers = ctk.CTkFrame(self.root,
fg_color=self.colors["panel2"], corner_radius=10)
        answers.pack(fill="x", padx=20, pady=5)

        highest_department =
self.incidents_df["Department"].value_counts().idxmax()
        highest_equipment = self.equipment_df.loc[
            self.equipment_df["Downtime"].idxmax(), "EquipmentID"]
        lowest_availability = self.equipment_df.loc[
            self.equipment_df["Availability"].idxmin(), "EquipmentID"]

        text = (
            f"• Department with most incidents: {highest_department}
|   "
            f"• Equipment with highest downtime: {highest_equipment}
|   "
            f"• Equipment with lowest availability:
{lowest_availability}"
        )
        ctk.CTkLabel(answers, text=text, justify="left",
                     font=("Arial", 11),
text_color=self.colors["text"]).pack(anchor="w", padx=15, pady=10)
        self.back_button()

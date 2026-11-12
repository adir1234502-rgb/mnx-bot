
import subprocess
import sys
import time
from datetime import datetime
import pytz

# שמות קבצי הבוטים שלך ב-GitHub
BOT_FILES = [
    "mnx_bot.py",
    "bot.py"
]

processes = {}

def start_bots():
    global processes
    for bot_file in BOT_FILES:
        if bot_file not in processes or processes[bot_file].poll() is not None:
            print(f"[STARTER] מפעיל את {bot_file}...", flush=True)
            processes[bot_file] = subprocess.Popen([sys.executable, bot_file])

def stop_bots():
    global processes
    print("[STARTER] נכנסה שבת - מכבה את כל הבוטים...", flush=True)
    for bot_file, proc in list(processes.items()):
        if proc.poll() is None:
            proc.terminate()
    processes.clear()

# הגדרת אזור זמן לישראל
israel_tz = pytz.timezone('Israel')

try:
    while True:
        now = datetime.now(israel_tz)
        weekday = now.weekday() # 4 = שישי, 5 = שבת, 6 = ראשון
        hour = now.hour
        minute = now.minute

        # בדיקה האם עכשיו שבת (מיום שישי ב-16:00 ועד מוצאי שבת ב-20:00)
        is_shabbat = False
        if (weekday == 4 and hour >= 16) or (weekday == 5) or (weekday == 6 and hour < 20):
            is_shabbat = True

        if is_shabbat:
            if processes: # אם שבת והבוטים דולקים, נכבה אותם
                stop_bots()
        else:
            if not processes: # אם שבת נגמרה והם כבויים, נדליק אותם חזרה
                print("[STARTER] אמצע השבוע - מפעיל את הבוטים מחדש...", flush=True)
                start_bots()
            else:
                # מעקב רגיל באמצע השבוע לוודא שאף בוט לא קורס
                for bot_file, proc in list(processes.items()):
                    if proc.poll() is not None:
                        print(f"[STARTER] {bot_file} נעצר. מפעיל מחדש...", flush=True)
                        processes[bot_file] = subprocess.Popen([sys.executable, bot_file])

        time.sleep(30) # בדיקה כל 30 שניות

except KeyboardInterrupt:
    stop_bots()

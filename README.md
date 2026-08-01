import json
from datetime import datetime
from pathlib import Path

DB_FILE = Path("activity_log.json")


class ActivityTracker:

    def __init__(self):
        if not DB_FILE.exists():
            DB_FILE.write_text("[]")

    def load(self):
        with open(DB_FILE, "r", encoding="utf-8") as f:
            return json.load(f)

    def save(self, data):
        with open(DB_FILE, "w", encoding="utf-8") as f:
            json.dump(data, f, indent=4)

    def add_activity(self):
        project = input("Project Name❤️: ")
        description = input("What did you do? ")
        minutes = int(input("Minutes spent: "))

        data = self.load()

        data.append({
            "project": project,
            "description": description,
            "minutes": minutes,
            "date": datetime.now().strftime("%Y-%m-%d")
        })

        self.save(data)

        print("Activity saved.")

    def today_report(self):
        today = datetime.now().strftime("%Y-%m-%d")

        data = self.load()

        today_data = [
            item for item in data
            if item["date"] == today
        ]

        total = sum(
            item["minutes"]
            for item in today_data
        )

        print("\nToday's Report")
        print("-" * 40)

        for item in today_data:
            print(
                f"[{item['project']}] "
                f"{item['minutes']} min"
            )
            print(item["description"])
            print()

        print("-" * 40)
        print(f"Total Time: {total} min")

    def project_stats(self):
        data = self.load()

        stats = {}

        for item in data:

            project = item["project"]

            stats[project] = (
                stats.get(project, 0)
                + item["minutes"]
            )

        print("\nProject Statistics")
        print("-" * 40)

        for project, minutes in sorted(
            stats.items(),
            key=lambda x: x[1],
            reverse=True
        ):
            print(
                f"{project:<20} "
                f"{minutes} min"
            )


def main():

    tracker = ActivityTracker()

    while True:

        print("\n=== Activity Tracker ===")
        print("1. Add Activity")
        print("2. Today's Report")
        print("3. Project Statistics")
        print("4. Exit")

        choice = input("Select: ")

        if choice == "1":
            tracker.add_activity()

        elif choice == "2":
            tracker.today_report()

        elif choice == "3":
            tracker.project_stats()

        elif choice == "4":
            break

        else:
            print("Invalid choice")


if __name__ == "__main__":
    main()

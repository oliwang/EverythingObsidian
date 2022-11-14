---
title: How to add tasks to Sorted³ from Obsidian with QuickAdd plugin
plugins:
- QuickAdd
---

I use @StaySortedApp to keep track of my simple one-step todos and @obsdmd for tasks that belong to projects. I really enjoy using sorted to hyper-schedule my events and tasks in the same timeline. I want to use it to schedule my project task as well but I don't want to enter the same information twice: once in Sorted and once in Obsidian. So I wrote a script to use quickadd to create a task with a sorted link in Obsidian. If I click on the link, the task is added to Sorted.

- [ ] Example Task #test Inbox  30 [📥](sorted://x-callback-url/add?title=Example%20Task&date=2022-11-14&tags=test&duration=30)  📅 2022-11-14

If you have Sorted³ installed on your device, click on the link and a new task will be added to your Sorted³ Inbox.

## Step 1: Install QuickAdd Plugin and enable it

![Installing QuickAdd](https://imagehosting-ow.oss-cn-hangzhou.aliyuncs.com/202210182233214.gif)

Settings - Community Plugins - Browse - QuickAdd

If you are new to Obsidian, don't forget to **enable** the plugin.

## Step 2: Create a Capture

![Create a Capture](https://imagehosting-ow.oss-cn-hangzhou.aliyuncs.com/202210182238281.gif)

I didn't turn on the Task option because I may have subtasks. You can turn it on or off based on how you write your tasks.

Add the below code to the Capture format. You may have a different list. Don't forget to modify your code. You can also format the output string differently.

````
```js quickadd
// getting user input
var task_title = await this.quickAddApi.inputPrompt("Task Info");
if (task_title == "" || task_title == undefined) {
	alert("Your task must have a title");
	return "";
}
var due_date_raw = await this.quickAddApi.inputPrompt("Due Date(YYYY-MM-DD) or (YYYY-MM-DD HH:mm)");
var tags = await this.quickAddApi.inputPrompt("Tags, use '#', seperate with space");
var duration = await this.quickAddApi.inputPrompt("Duration (minute)");
var list = await this.quickAddApi.suggester(
	["Inbox", "🗂AddToNote", "💻Create", "👩‍💻Code"],
	["Inbox", "🗂AddToNote", "💻Create", "👩‍💻Code"]
);

// handle optional input and encode " " and "#" in string
tags = tags ? tags : "";
duration = duration ? duration : "";
var due_date = due_date_raw ? due_date_raw : "";

var date_in_task = "";
var task_date = "";
var task_time = "";

if (due_date != "") {
	if (due_date_raw.includes(" ")) {
		task_date = due_date_raw.split(" ")[0];
		task_time = due_date_raw.split(" ")[1];
	} else {
		task_date = due_date_raw;
	}
	date_in_task = ` 📅 ${task_date}`;  
}

var task_title_encoded = encodeURIComponent(task_title);
var tags_encoded = tags.replaceAll("#", "").replaceAll(" ", ",");
var list_encoded = list.replaceAll(" ", "%20");


var list_in_link = "";
if (list != "Inbox") {
	list_in_link = `&list=${list_encoded}`;
}


var due_date_in_link = "";
if (due_date) {
	due_date_in_link = `&date=${encodeURIComponent(due_date_raw)}`;
}


var tags_in_link = "";
if (tags_encoded) {
	tags_in_link = `&tags=${tags_encoded}`;
}
var duration_in_link = "";
if (duration) {
	duration_in_link = `&duration=${duration}`;
}

// format the output string
var result = `${task_title} ${tags} ${list} ${task_time} ${duration} [📥](sorted://x-callback-url/add?title=${task_title_encoded}${list_in_link}${due_date_in_link}${tags_in_link}${duration_in_link}) ${date_in_task}`

return result;

```
````

## Step 3: Create a new task and click on the link

![Create New task and send to sorted](https://imagehosting-ow.oss-cn-hangzhou.aliyuncs.com/202210182253657.gif)

You will see your new task appear in Sorted³. The tasks you created can also be queried with the Obsidian [Tasks](https://github.com/obsidian-tasks-group/obsidian-tasks) plugin and the [Dataview](https://github.com/blacksmithgu/obsidian-dataview) plugin.

## Optional Step: Add Hotkey

![Change Hotkey](https://imagehosting-ow.oss-cn-hangzhou.aliyuncs.com/202210182248962.gif)

Settings - Hotkeys - Find your Capture Command and change the hotkey. You may need to resolve some conflicts.

I will be using this quite a lot so I'm using `Command+T` for this.

I think as I know more about QuickAdd, I can probably optimize the workflow.


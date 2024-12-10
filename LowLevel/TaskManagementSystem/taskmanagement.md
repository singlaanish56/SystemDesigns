Task Management System


# Requirements

The task management system should allow users to create, update, and delete tasks.
Each task should have a title, description, due date, priority, and status (e.g., pending, in progress, completed).
Users should be able to assign tasks to other users and set reminders for tasks.
The system should support searching and filtering tasks based on various criteria (e.g., priority, due date, assigned user).
Users should be able to mark tasks as completed and view their task history.
The system should handle concurrent access to tasks and ensure data consistency.
The system should be extensible to accommodate future enhancements and new features.


# Entities and Classes

1. User

 username, email, group, list->tasks, list->userActions, list->Permissions

list of tasks can be used of find quickly find asll tasks, and then filter between them
userActions or history is the recent set of actions taken, this could be used for history when the user clicks on their profile

2. User Management  <Singleton>

From code perspective this provided API's to handle user registration / login/ logout. This will also provide the API to handle and mnodify the user information, such as interacting with the list


3. Groups, groupName, map->users/permissions,

usually in a business setting the employees belong to a particular team which helps identifies their aread of work.
At default this group can be assigned, then the user wont have the permission to do tasks created under that group


4. Group Management <Singleton>

Similar to handle the group CRUD operations, as well as removing /adding memebers, updating the permission level of the members


4. TaskStatus

this is an  enum with the tasks stated pending, in progress, completed.
used by the other entities or classes in the codebase


5. Task

this is the core entity of the codebase, has the information as id, title, description, priority, status, endDate / startDate, timeEstimated,  map<user,list<>> timeloggedActions, assignedUser, map<actio, list->observers> anyaction, list<Task> subTasks, list<Tasks> dependencies

this is the structure, tasks can have subtasks, which inherently can have more subtasks
the project level are also tasks, which in turn can have sublevel tasks

We have a map of oberservers as well, which has key as action, and the list of people wanting to be notified.
This allowd many feature, 

watch this task, anyone can add themselves to be notified of any updated on the task,
(how can be easily configured by user in the profile, email / in app task managment syste, notifs)

other tasks may have added this as a dependency to start, so when you mark it as done, that task needs to notified too

when you compelted all the subleveltasks, the parent task should be marked done as well, so when you mark subtasks as done , the parent task is notified



6. Task Management <Singleton>

tis singleton specifcally manages the CRUD operation on tasks, as well other API as notifying the observers etc.
This also makes the txns thread safe, by acquiring locks a ndmutexed while doing an updare to the tasks to ensure a good concurrent access

this can also handle the search operation on the various tasks

7. Permission Management <Singleton>

this is a thought, this manager helps whether the user is authorized to perform an operation.
can be used by the frontend to know whether or not to display the information to the user.

for eg. maybe user from another group can see the task, but add cant log time it.

this can be handled with the group user belongs, to and the group the given tasks belongs to. To allow the user to do any changes, you can add them as part of the group.

Otherwise, any top level group will have the control of lower level group, The group of the project manager vs the group of a developer.


8. Task management System <Singleton>

Finally a singleton class, that will control all the other classes.
//also provide the testing interface for the interview purpose.


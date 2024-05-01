# Collection of thoughts about the Data Set:

## First observations:

My first move was observing the start and the end activities and i have discovered the following result:
```
Start activities: 
    {'Create Fine': 150370}
End activities: 
    {'Send Fine': 20755,
     'Send for Credit Collection': 58997,
     'Payment': 67201, 
     'Send Appeal to Prefecture': 3144, 
     'Appeal to Judge': 134, 
     'Notify Result Appeal to Offender': 86, 
     'Receive Result Appeal from Prefecture': 53}
```
As we all expected a RTF (**Road Traffic Fine**) starts with a `Create Fine` activity and can end in many ways.

Usually we think that a RTF should end with a payment in majority, and this observation confirm this thought, but let us discover that a great percentage of RTF also ends up with `Send for Credit Collection` or (the third largest number) `Send Fine`.

We should consider that an RTF that ends up with `Send Fine` is not a real end for our process, but just a checkpoint in the workflow. We should think the same for `Send for Credit Collection`, in fact it seems like another checkpoint that should end up into `Payment`, in this moment I am considering this activity like a reminder for the unpayed RTF, but I will make it sure in the future, analyze the time between a `Send Fine` and a `Send for Credit Collection` activity.

Then I decided to have a global look at the percentage of all the activities, not considering them as Start/End points, and I have produced this bar graph with a percentage for each of the bar:

![Percentage of activities in the dataset](image.png)

From a first look I saw how the activity `Create Fine` is only a Start one, and I have to exclude if I find it in the middle of a flow.

Then I confirmed my previous thought, the one where I was thinking about `Send Fine` as a checkpoint, in fact there are more than 100,000 of it in the Event Log and only, more or less, 20,000 as End Activity.

I cannot say the same for `Send for Credit Collection` unfortunatly. The numbers confute my theory, and I am now considering this activity as a real End one.

I am also surprised by the activities `Add penalty` and `Insert Fine Notification` that, observing this graph, seems to be part of most of the work flow of an RTF.

## First observation in PMTK:

Then i switched from the notebook into PMTK, seeking for confirm and more information in a easy way. So i decide to have a look into the *Log Summary*:

![PMTK Log Summary](image-1.png)

The summary confirmed me what I have seen before, adding informations regarding the number of events per case, revealing the most common cases and helping me to understand which infos are usefull are which are not. In this case I am thinking to drop all of the cases that involve more than 7 cases. 

Then I had a look at the outliers, that compared to the total number of cases are a ridicolous amount, and they do not provide any usefull infos.

Then I took a look into the *Throughput Time* section:

![Throughput Time](image-3.png)

As I have done before I didn't include the outliers in the image, but I have to say that in this case they are more than what I expected so, maybe, they deserve a look.

For god sake a good percentage of events (~48.13%) end up with a "fast" resolution, but there is also a lot of events that are not so "fast" even if we exclude the outliers values (~50 months and more) so, again, I may consider to cut off some period to have a better analysis (I am thinking to consider only the events lower than 35 months).

Then my eyes focused on the *Bottlenecks* section, where I found out 4 main transition that are time critical, but the occurences of these transition is so low that it is not a big issue...

So I have decided to sort the bottlenecks by occurrences:

![Bottlenecks sorted for occurrences](image-4.png)

Even if I consider wrong that it is so common let pass 2 months between the creation of a fine and the actual fine, this bottlenecks is not so huge in terms of time. Instead, the highlighted one is really important for my analysis, it is suggesting me that if the credit collection was done right after the adding of a penalty we can save up so much time!

I also investigated on the `Send for Credit Collection` and I have discovered (as we seen before) that it is, mostly, a final state of the event, with only 16 occurences that involve it as a transition state:

![Send for Credit Collection as a transition state](image-5.png)

As I said earlier we can ignore this bottlenecks for the small number of occurences, but we cannot ignore how this final state is reached to slowly, it is the first real problem of this process...
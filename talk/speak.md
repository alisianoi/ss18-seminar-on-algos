Hello everyone! My name is Aleksandr Lisianoi and today I am going to
talk about "Scheduling Jobs across Geo-Distributed Datacenters with
Max-Min Fairness". This talk is primarily based on two papers, both of
which you can now see on the slides. The first paper provides the
context and poses a real-world scheduling problem which is then
formalized as a mathematical optimization problem. The first paper
also shows how to transform that optimization problem into an
equivalent problem from a known class of optimization problems. This
transformation allows to effectively solve the original
problem. Finally, the first paper concludes with a synthetic
experiment that shows the advantages of being able to solve the
original problem.

As for the second paper, it provides the necessary mathematical
background and describes both the class of optimization problems as
well as references a technique that allows to efficiently solve them.

My plan for the talk today consists of four parts. I will begin with
an informal introduction and provide some motivational examples. Then
in the second part I will present and explain the optimization
problem. In that same part I will also describe the transformation
sequence which shows that the initial optimization problem could be
reduced to an optimization problem from a class of known optimization
problems. Then in the third part I will describe the experimental
setup which validates the usefullness of the proposed mathematical
model. And finally I will talk a little bit about the presented
results.

Let me begin with the main question. This question could be formulated
as follows: "Given a large amount of data distributes across
geographically separate datacenters, how to efficiently run multiple
analytical jobs in a fair fashion?" I have emphasized several parts of
this question and would like to talk a little bit about those part
now.

Let us first take a look at some typical examples of analytic jobs.

The first example comes from 2007 when a system administrator at The
New York Times was faced with the following problem: the newspaper
offered its readers a free online service where they could request an
old issue of the paper and get its digital version almost
immediately. To do that, The New York Times scanned all of their past
papers which amounted to approximately 4 terabytes of imagery. Each
time the user would request a paper, a backend task would fetch the
appropriate scans and glue them together to form a pdf document and
return it to the user.

Now, the online newspaper websites are typically sensitive to sudden
spikes in activity when there is a major news story. During those
spikes it is very important that the webservers do not have to perform
unnecessary work like dynamically generating pdf files on demand. So,
it meant that the administrators had to preprocess approximately 4
terabytes of images and serve the pdf files statically to. Back in
2007 it meant using a Hadoop cluster because otherwise it would just
take to much time. With the cluster, however, the process could be
finished in under 24 hours. And now this servers as a very simple
example of an analytic job.

Moving forward in time to 2017, you can see a completely different
picture. The rather well-known company Spotify offers its users a
service where they can listen to a vast selection of all kinds of
music for a subscription fee. Part of the service is a recommendation
system that can suggest new music to a Spotify user based on the
sparse vector of music which that user has already listened to and the
overlap between tastes of similar users. To achieve that, the company
runs a modified version of the alternating least squares algorithm,
which is a machine learning algorithm that basically alternates
between rows and columns of a huge sparse matrix of users vs music and
predicts values in that matrix.

My point here is that the analytic jobs have become a lot harder and
require a lot more resources.

Finally, let's talk about fairness. To begin with, fairness
intuitively is a situation when all the participants get a fair share
of the available resources. As for Max-Min fairness specifically, it
is a situation when one cannot increase the share of some
participant's resources without decreasing the non-larger share of
some other participant. There are many possible scheduling strategies, like:

1. Maximum throughtput scheduling, which preferes to allocate
resources for the smallest jobs from the job queue first, leading to
high job completion rates but long wait times for large jobs.
2. First-come first-served scheduling, which allocates available
resources to those jobs that are queued first, and then subsequent
jobs have to wait until some of those resources are freed. If any job
"misbehaves", then subsequent jobs suffer the consequences.
3. Max-Min fairness, which allocates resources to jobs as those jobs
arrive by decreasing the share of resources for the existing jobs.

Alright, we've talked a bit about the context, we've seen a couple
typical examples of anaylitic jobs.

Now let us move on to actually formulate the mathematical model. The
main paper defines several sets, here they are. These are datacenters,
they are numbered, here is the capacity of a datacenter which in this
model just equals the number of tasks the datacenter can run at the
same time. This is the set of analytic jobs, each of which has a
corresponding set of tasks. Now, pay attention as each job generally
speaking has a different number of tasks. Finally, here is the
execution time of a particular task i from a particular job k if that
task is assigned to a particular datacenter j. And this one is the
total time it takes to move all the data which is required for task i
from potentially other datacenters into datacenter j.

Here is a chain of three definitions for lexicographic order on
verctors of numbers and lexicographic minimization. These definitions
are more or less what you would expect but there is one small trick
which is better demonstrated on the optimization problem itself. Which
is why let us quickly move on to the problem so that I could show it.

Here is the first optimization problem, which is an instance of
lexicographic optimization. It works in the following fashion: first,
for each possible assignment of a task to a datacenter you compute the
completion time. Then for each job (which is a group of tasks) you
find the task with the longest completion time. You sort these longest
completion times in a non-decreasing order and then lexicographically
minimize the sorted vector.

The third constraint represents the finite resources that the
datacenter has. Specifically, each datacenter is characterized by the
number of tasks in can run.

The forth constraint tells us that each task is assigned to exactly
one datacenter. The combination of the third and the forth constraint
tell us that all the datacenters combined have the capacity to run all
the tasks in parallel.

Finally, constraints 5 will also later be called integrality
constraints because it says that the assignment variable is either
zero or one, in other words it is an integer.

Now, this problem is not an easy or known optimization problem.
However, the main paper shows a series of transformations which
produce an equivalent problem which belongs to the class of
optimization problems described in the second paper. That class of
problems is defined by the following characteristics:

The cost function must be a separable and convex function. In this
case, separability means that it is possible to represent a function
of several arguments as a sum of functions each of which depends on
just a single argument. The definition of convexity is also on the
slide.

The constraints must form a totally unimodular matrix. It means that
every square submatrix of the matrix of constraints must have a
determinant that is equal to minus one, zero or one.
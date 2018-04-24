Hello everyone! My name is Aleksandr Lisianoi and today I am going to
talk about "Scheduling Jobs across Geo-Distributed Datacenters with
Max-Min Fairness". This talk is primarily based on two papers, both of
which you can now see on the slides. The first paper talks about a
fair and optimal way of scheduling jobs on a distributed cluster. In
particular, it formulates a mathematical optimization problem, then
solves it and validates the solution in a synthetic experiment. As for
the second paper, it provides the necessary mathematical background to
actually solve the optimization problem.

My plan for the talk today looks like this. I will begin with an
informal introduction and provide some motivational examples. Then I
will present and explain the optimization problem from the first
paper. In that same part I will also describe the transformation
sequence which shows that the initial optimization problem could be
reduced to an optimization problem from a class of known optimization
problems. This will be followed by a description of the conducted
experiments and their results.

Let me begin with the main question. This question could be formulated
as follows: "Given a large amount of data distributed across
geographically separate datacenters, how to efficiently run multiple
analytical jobs in a fair fashion?" There are four parts of this
question that I have emphasized and would like to briefly talk about.

First and foremost, let's have a look at some typical examples of
analytic jobs.

The first example comes from 2007. It is about a system administrator
at The New York Times who was faced with the following problem. The
newspaper offered its readers a free online service where they could
request an old issue of the paper and get its digital version almost
immediately. To be able to do that, The New York Times did a few
things. First, they scanned all of their old papers which amounted to
approximately 4 terabytes of imagery. Each time a user would request
a paper, a backend task would fetch the appropriate scans and glue
them together to form a pdf document and return it to the user.

Now, the online newspaper websites are typically sensitive to sudden
spikes in activity when there is a major news story. During those
spikes it is very important that the webservers do not have to perform
unnecessary work, exactly like this dynamic generation of pdf files on
demand. To optimize this, the administrators had to preprocess
approximately 4 terabytes of images and serve the pdf files statically
instead. Back in 2007 this meant using a Hadoop cluster because
otherwise it would just take too much time. With the cluster, however,
this simple analytic job was finished in under 24 hours.

Fast forward to 2017. Let's take a look at the rather well-known
company Spotify which offers its users an online service where they
can listen to a vast selection of music for a subscription fee. Part
of the service is a recommendation system that can suggest new music
to a Spotify user based on the sparse vector of music which that user
has already listened to and the similarity between this user and other
users. To achieve that, the company runs a modified version of a
machine learning algorithm called alternating least squares.

My point here is that the analytic jobs can be very different and they
have generally been getting more difficult over the years.

Alright, let's move on to the next couple of characteristics from the
original question. Specifically, let's talk about what it means to be
dealing with a large amount of data. Well, it means that the location
of the data starts to play a big role in scheduling. In other words,
data locality matters because the closer the data is to the node that
runs the computation, the faster the completion time. This is the
statement I have seen in several other papers that focus on job
scheduling. What catches the eye about this particular paper, however,
is the fact that it talks about several geographically separated
datacenters. This makes data locality even more important to consider,
so much so that network speed between the datacenters becomes a
prominent factor. So, the optimization problem that will be revealed
shortly must take it into account.

Finally, let's talk a bit about fairness, because the original
question mentioned a fair way to schedule the jobs. Now, intuitively,
fairness is a situation when all the jobs get a fair share of the
available resources. When it specifically comes to Max-Min fairness,
there is a nice informal way to understand it. It is a situation where
you are forbidden from taking the resources from the "poor" jobs and
giving those resources to the "rich" jobs. There are also other types
of fairness, but I will not get into those.

# There are many possible scheduling strategies, like:

# 1. Maximum throughtput scheduling, which preferes to allocate
# resources for the smallest jobs from the job queue first, leading to
# high job completion rates but long wait times for large jobs.
# 2. First-come first-served scheduling, which allocates available
# resources to those jobs that are queued first, and then subsequent
# jobs have to wait until some of those resources are freed. If any job
# "misbehaves", then subsequent jobs suffer the consequences.
# 3. Max-Min fairness, which allocates resources to jobs as those jobs
# arrive by decreasing the share of resources for the existing jobs.

Alright, after talking a bit about the main question, let us take a
look at the way the authors of the first paper formalized it as an
optimization problem.

They begin with the definitions. They denote the set D to represent
the datacenters, numbered from one to J. For each datacenter, there is
the notion of its capacity, denoted by lowercase a and an index of the
datacenter. The analytic jobs are represented by the set K and
numbered from one to K. Each analytic job could be viewed as a set Tau
k of parallel tasks numbered from 1 to n_k. Generally speaking,
different jobs consist of a different number of parallel tasks.

Finally, the execution time of a parallel task i of an analytic job k
in datacenter j is denoted as lowercase e. At the same time the
network transfer time, which was mentioned as an important factor, is
denoted as lowercase c. This is the time that is required to transfer
the necessary data to datacenter j if the parallel task is to be
executed there.

Let's move on. On the next slide there will be an optimization problem
that deals with lexicographic minimization. This is why this slide
contains the necessary formal definitions. However, I will not go into
detail here and instead spend more time on the optimization problem
itself.

Here it is. This is the initial optimization problem from the first
paper. First, let's actually take a look at constraint number
two. This constraint works in the following way. First, an analytic
job k is chosen. Then for all of the tasks of this analytic job, all
possible datacenter assignments are tried. For each such assignment,
the total execution time, which is this sum, is computed. After that,
the maximum completion time is assigned to the variable tau_k.

Each analytic job is now represented by the worst completion time of
some of its task. There are K jobs in total, so there are K
values. These values are sorted from largest to smallest and put in a
vector f. This vector is lexicographically minimized, meaning that the
first one position is minimized first, then the first two and so on.

As for the other constraints, the third constraint represents the
finite resources that the datacenter has. Specifically, each
datacenter is characterized by the number of concurrent tasks that it
can run.

The forth constraint demands that each task is assigned to exactly one
datacenter. The combination of the third and the forth constraint
actually implies that all the datacenters combined have the capacity
to run all the tasks in parallel.

Finally, let's take a look at constraint 5. This constraint plays the
role of an indicator: it is one if task i of analytic job k is in the
end assigned to datacenter j and zero if it is not. This constraint is
also referred to as the integrality constraint, because its value is
just an integer.

Now, this problem is not an easy or known optimization problem.
However, the main paper shows a series of transformations which
produce an equivalent problem which belongs to the class of
optimization problems. This class is studied in more detail in the
second paper. Allow me to briefly mention the characteristics of that
class:

The cost function must be a separable and convex function. In this
case, separability means that it is possible to represent a function
of several arguments as a sum of functions each of which depends on
just a single argument. The definition of convexity is also on the
slide.

The constraints must form a totally unimodular matrix. It means that
every square submatrix of the matrix of constraints must have a
determinant that is equal to minus one, zero or one.

Alright, the solution to the optimization problem yields an assignment
of tasks to datacenters. This assignment lexicographically minimizes
the vector of completion times which are ordered from largest to
smallest. So, this assignment is indeed Max-Min fair.

The question that the authros of the first paper then ask is this: is
such task scheduling actually better than the default scheduling of
Apache Spark.

Here are the relevant architectural parts of Apache Spark: it takes an
analytic job and describes it as a directed acyclic graph of
tasks. This graph is then submitted to the so called DAG scheduler.

This schedular divides the graph into consequitive stages and then
submits each stage to the task scheduler. The authors of the first
paper implemented their own task scheduler. Then they chose sorting as
an analytic job and ran three experiments: with three, four and five
concurrent sorting jobs.

They ran their experiment on a cluster of 12 Amazon Elastic Computing
Instances, putting two machines into each of the 6 geographically
distinct datacenters. They also disables YARN, which is an external
resource manager.

The resulting measurements are summarized in this graph. The dashed
line is the default Apache Spark scheduler and the solid line is the
custom scheduler that determines the task assignment by solving the
optimization problem. Each column represents the results for 3, 4 and
5 consequtively running sorting jobs. The upper row shows the largest
worst completion time and the lower row shows the second largest worst
completion time among all tasks.

As the authors point out, in each case the worst completion time of
the baseline is larger than that of the custom scheduler. Based on
that they show the usefullness of the approach.

Which brings me to the conclusion. Personally, I was very impressed
with the mathematic theory behind the algorithm. However, the
experiment, in my opinion, could be done a little bit better. In
particular, the authros use a legacy Sort example application, which I
could only find for the python bindings to Apache Spark. I also could
not find the implementation of the schedular. There are probably
several good reasons for that (for example, the authors indicate a use
of a third-party optimization library) but it would be nice to be able
to see it nonetheless.

It is also not clear how the four and five parallel jobs test
scenarious differ, because the paper says that the 5 jobs case did not
actually run 15 tasks but 12. Also, it is not actually clear what the
X axis on the completion time graphs is.
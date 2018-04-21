Hello everyone! My name is Aleksandr Lisianoi and today I am going to
talk about "Scheduling Jobs across Geo-Distributed Datacenters with
Max-Min Fairness". This talk is primarily based on two papers. The
first paper that you see on the slide explains the whole context,
formulates a formal optimization problem, solves it and then performs
experiments that validate the usefullness of the solution. The second
paper actually provides the necessary mathematical background to solve
the optimization problem.

Let me begin with the main question that the authors of the first
paper are addressing: "Given a large amount of data distributes across
geographically separate datacenters, how to efficiently run multiple
analytical jobs in a fair fashion?" There are several things I would
like to talk about before this question is transformed into a formal
optimization problem.

First of all, what is a large amount of data and what is a typical
example of an analytic job? To get an idea, let's quickly take a look
at these two real world examples.

The first story comes from 2007 when a system administrator at The New
York Times was faced with the following problem: the newspaper offered
its readers a free online service where they could request an old
issue of the paper and get its digital version almost immediately. For
that, The New York Times scanned all of their past papers which
amounted to approximately 4 terabytes of imagery. Each time the user
would request another paper, a backend task would glue several images
together to form a pdf and return it to the user.

Now, the online newspaper websites are typically sensitive to sudden
spikes in load when there is a major news story. So, anything that
adds to that load should be optimized. Back in 2007 even the simple
task of glueing together 4 terabytes of images would take too long on
a couple of machines. So, the system administrator got away with using
Apache Hadoop to complete the task in under 24 hours.

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

Going back to the original question, let's quickly mention
geographical separation. In one sentence, it means that the connection
speed between your datacenters matters. Going back to the Spotify
example for a second, the used to be a time when their Hadoop cluster
fit in a single room in a conventional apartment. This is not the
scale at which the problem from the main paper matters. These days,
however, when Spotify operates one of the largest Hadoop clusters in
Europe, has also their servers present in datacenters across the globe
and runs approximately 20 thousand analytic jobs daily, their use case
is applicable for the optimization problem you are about to see.

Finally, let's talk about fairness. 
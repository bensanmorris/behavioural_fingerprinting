# behavioural_fingerprinting
The idea is to apply machine learning to the detetction of compromised applications by comparing the behavioural fingerprint of an application with an established behavioural fingerprint obtained for the application in a clean room environment.
Machine learning is applied in order to estimate a probability that the application is genuine i.e. its behaviour doesn't deviate from the expected behaviour by comparing a vectorised representation of an application's behaviour (a behavioural fingerprint) with the established fingerprint for the application.

# Algorithm
- Vectorise file activity performed by an application (and it's constituent files). A file system driver will assist by intercepting file system access, obtaining the originating application / application file performing the file access, convert this file path into a key code that will be used as an index into a sparse matrix (i.e. a "cell" in the matrix that represents this unique file / file's hash), colour the cell according to the type of access being performed by the file i.e. RED = READ, GREEN = WRITE, BLUE = ADD, ALPHA = DELETE. Each color channel in the cell's value will be a monotonically increasing value.
- Encode this vectorisation into sparse matrix form
- Collect, for a given application, multiple vectorised fingerprints accross many machines and users
- Train a model on this fingerprint data
- Perform prediction of the application's behaviour on machines / users not in the training set against the trained application behaviour model

# Vectorising the file system
It is not known yet if the hierarchical nature of an application's files and the areas of the file system it touches form strong features for detection so ideally the key code that uniquely identifies an application's file touching the file system might be some form of hierarchical hash (i.e. the key code being obtained via a z-order curve representation of the file's path or some other method that maintains / encodes the hierarchy implcicit in a file's path).

# Prototype 1
- Choose an algorithm that converts a file's path into a unique key code (and the probability of collision i.e. 2 or more file paths result in the same key code)
- Implement a simple file system filter driver
- Generate a sparse matrix of activity as explained above for a single fixed application (MS Word for instance)
- Repeat the sparse matrix construction on several machines / users
- We may just at this stage consider the fingerpint mean and standard deviations to see how volatile fingerprints accross machines, users and time intervals are.


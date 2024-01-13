# Behavioural Fingerprinting - Pre-emptive malware monitoring
The idea is to apply machine learning to the classification and detection of compromised applications by comparing the behavioural fingerprint of an application with an established behavioural fingerprint obtained for the application in a clean room environment.
Machine learning is applied in order to estimate a probability as to how genuine the application is behaving by comparing a vectorised representation of an application's behaviour (a behavioural fingerprint) with the established fingerprint for the application.

[My original presentation video](https://youtu.be/ZaBt7gynkx4) - I had to keep it under 3 minutes so apologies for the speed of delivery.

# Approach
- Vectorise file activity performed by an application (and its constituent files). A file system driver will assist by intercepting file system access, obtaining the originating application / application file performing the file access, convert this file path into a key code that will be used as an index into a sparse matrix (i.e. a "cell" in the matrix that represents this unique file / file's hash), colour the cell according to the type of access being performed by the file i.e. RED = READ, GREEN = WRITE, BLUE = ADD, ALPHA = DELETE. Each color channel in the cell's value will be a monotonically increasing value.
- Encode this vectorisation into sparse matrix form ([this may help](https://github.com/google/neural-tangents))
- Collect, for a given application, multiple vectorised fingerprints accross many machines and users
- Train a classifier on this fingerprint data for each application

NB. Future iterations might look at other sources of application behaviour using a similar approach (network access for instance).

# Vectorising the file system
It is not known yet if the hierarchical nature of an application's files and the areas of the file system it touches form strong features for classification / detection so ideally the key code that uniquely identifies an application's file touching the file system might be some form of hierarchical hash.

# Prototype 1

For the initial prototype we use supervised learning as follows:

1. We implement a file system driver that intercepts file activity and inserts it into a database as follows
   1. Originating application
   2. The file being touched
   3. The file activity type (i.e. read, write, add or delete)
   4. Timestamp
2. We launch an application (i.e. MS Word) on machine A with this file system driver / database installed
3. A user then uses the MS Word for a period of time whilst we collect this data
4. When the collection period is over we filter the database so we keep only the activity for the application of interest (MS Word)
5. We export this data (in csv format) and label it as being MS Word
6. We repeat collection, filtering, export and labelling of MS Word accross N users / machines
7. We then split the collected data into training / test sets
8. We train a classifier on the labelled MS Word data training data
9. We then test the test set data against the classifier and see what the confidence level looks like

# Refs:

(Application identity)

- [Checksums and checksum-based identity vulnerabilities](https://carleton.ca/scs/wp-content/uploads/TR-04-09.pdf)

(Application defense mechanisms)

- [WDAC - Windows Defender Application Control](https://learn.microsoft.com/en-us/windows/security/application-security/application-control/windows-defender-application-control/wdac) - requires manual ongoing configuration / policy administration

(Filter drivers)

- https://learn.microsoft.com/en-us/windows-hardware/drivers/ifs/
- https://learn.microsoft.com/en-us/windows-hardware/drivers/samples/file-system-driver-samples     https://github.com/microsoft/windows-driver-samples/tree/main/filesys/miniFilter/minispy
- https://learn.microsoft.com/en-us/windows-hardware/drivers/ifs/filter-manager-concepts
- https://stackoverflow.com/questions/16702971/how-to-trap-file-access-attempts-with-a-filter-driver-kernel-and-offer-dialog
- https://www.codeproject.com/Articles/43586/File-System-Filter-Driver-Tutorial

(DLL injection attacks)

- [An example of DLL injection (may come in handy when testing how an application's fingerprint changes in response to a rogue dll loaded into the app's process)](https://www.codeproject.com/Tips/5369555/Inject-DLL-in-Another-Process-using-Cplusplus-Win3)

# Behavioural Fingerprinting - Pre-emptive malware monitoring
The idea is to apply machine learning to the classification and detection of compromised applications by comparing the behavioural fingerprint of an application with an established behavioural fingerprint obtained for the application in a clean room environment.

[My original presentation video](https://youtu.be/ZaBt7gynkx4) - I had to keep it under 3 minutes so apologies for the speed of delivery.

# Prototype 1

For the initial prototype we use supervised learning as follows:

1. We implement a file system driver that intercepts file activity and inserts it into a database as follows
   1. Originating application
   2. The file being touched
   3. The file activity type (i.e. read, write, add or delete)
   4. Timestamp (the timestamp won't form part of the initial training data)
2. We launch an application (i.e. MS Word) on machine A with this file system driver / database installed
3. A user then uses the MS Word for a period of time whilst we collect this data
4. When the collection period is over we filter the database so we keep only the activity for the application of interest (MS Word)
5. We export this data (in csv format) and label it as being MS Word
6. We repeat collection, filtering, export and labelling of MS Word activity across N users / machines
7. We then split the collected data into training / test sets
8. We train a classifier on the labelled MS Word data training data (any non-numeric data i.e. Application / File paths could be translated to hashes with a reverse lookup table maintained so we can recover the original value from the hash).
9. We then test the test set data against the classifier and see what the confidence level looks like

# Refs:

(Application identity)

- [Checksums and checksum-based identity vulnerabilities](https://carleton.ca/scs/wp-content/uploads/TR-04-09.pdf)

(Application defense mechanisms)

- [WDAC - Windows Defender Application Control](https://learn.microsoft.com/en-us/windows/security/application-security/application-control/windows-defender-application-control/wdac) - requires manual ongoing configuration / policy administration

(Filter drivers)

- https://github.com/apriorit/file-system-filter
- https://learn.microsoft.com/en-us/windows-hardware/drivers/ifs/
- https://learn.microsoft.com/en-us/windows-hardware/drivers/samples/file-system-driver-samples     https://github.com/microsoft/windows-driver-samples/tree/main/filesys/miniFilter/minispy
- https://learn.microsoft.com/en-us/windows-hardware/drivers/ifs/filter-manager-concepts
- https://stackoverflow.com/questions/16702971/how-to-trap-file-access-attempts-with-a-filter-driver-kernel-and-offer-dialog
- https://www.codeproject.com/Articles/43586/File-System-Filter-Driver-Tutorial

(DLL injection attacks and hooking)

- [An example of DLL injection (may come in handy when testing how an application's fingerprint changes in response to a rogue dll loaded into the app's process)](https://www.codeproject.com/Tips/5369555/Inject-DLL-in-Another-Process-using-Cplusplus-Win3)
- https://www.apriorit.com/dev-blog/reverse-engineer-api-calls-and-write-hooks
- https://www.apriorit.com/dev-blog/160-apihooks

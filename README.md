# behavioural_fingerprinting
The idea is to apply machine learning to the detetction of compromised applications by comparing the behavioural fingerprint of an application with an established behavioural fingerprint obtained for the application in a clean room environment.
Machine learning is applied in order to estimate a probability that the application is genuine (i.e. its behaviour doesn't deviate from the expected behaviour by comparing a vectorised representation of an application's behaviour (a behavioural fingerprint) with the established fingerprint for the application.

# Algorithm
- Vectorise file activity performed by an application (and it's constituent files)
- Encode this vectorisation into sparse matrix form
- Collect, for a given application, multiple vectorised fingerprints accross many machines and users
- Train a model on this fingerprint data
- Perform prediction of the application's behaviour on machines / users not in the training set against the trained application behaviour model

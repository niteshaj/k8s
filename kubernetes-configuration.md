Different applications would use different values for the app label, but a multi-tier application would additionally 
need to distinguish each tier. The frontend could carry the following labels:

    labels:
        app: guestbook
        tier: backend
        role: master
        track: stable  #will help Canary deployments stable release would have a track label with value as stable
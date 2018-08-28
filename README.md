## linuxserver
In this project, the catalog application done in the previous part of the course is deployed to an AWS Lightsail instance.
The project is hosted on apache2 with mod_wsgi python version 2.7.12. Slight modification is made in the catalog application source code to accomodate the requirements of apache 2.

### SSH to the instance
```
$  ssh -i linuxCourse grader@18.216.218.156
```

### Catalog App URL
http://18.216.218.156.xip.io/
xip.io domain name service is used to accomodate google oauth requirement of not having ip only url as redirect url.

### Summary of installed software and config changes

### Third party resources used


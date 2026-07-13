
Web awesome setup for pro
https://webawesome.com/workspaces/GLpRRY17NsxE/projects/uKAeRKe9SOSq

https://us.posthog.com/login

---

AWS

adding image content to S3 at this bucket https://us-east-1.console.aws.amazon.com/s3/buckets/cdn-lawbrokr?region=us-east-1&tab=objects

then go inside img folder
cretae neew folder for customer if it doesnt exist (all lowercase)
uplosd to that folder
convert images first to web optimized versions webp (research npm optimize package)

upload images, all lowercase, 

default path 
https://cdn.lawbrokr.com/img/logo.svg

theresn an image upload delay

base url 
https://cdn.lawbrokr.com/img/mycroft/mycroft-logo.svg

[mycroft-logo.svg](https://us-east-1.console.aws.amazon.com/s3/object/cdn-lawbrokr?region=us-east-1&prefix=img/mycroft/mycroft-logo.svg)

https://cdn.lawbrokr.com/img/lawbrokr/partners/alh-law-group.webp

https://cdn.lawbrokr.com/img/


---

context7 API key
```
ctx7sk-467171ce-e9cc-484a-950e-2df5eab684ab
```



---

/Users/chriskinsman/Documents/GitHub/boxii-js/src/customers/mycroft/index.ts
this is where posthog analytics gets configured

/Users/chriskinsman/Documents/GitHub/boxii-js/test/integration/sites/plain/index.html

claude code add extra hight effort

at the start of each session run 
/model (opus)
/effort (xhigh)

tab until you get auto mode
then in prompt always include following 2 items
- Please carefully review my instructions and ask me questions before proceeding to make sure you have a complete understanding of the task.
- Its very important that after you have completed your changes you perform the integration tests and make user that your changes work correctly on the different integration sites.

Also clear context (/clear) after each prompt


I found an issue with the mycroft boxii instance when testing. The testimonial widget has an issue where the cta text 'Customer story · Read more' is getting cut off and pushed outside of the bounding box. As well it appears that the font is not being applied correctly but I may be mistaken. I have taken a screenshot of this issue at @testimonial_glitch.png

Please carefully review my instructions and ask me questions before proceeding to make sure you have a complete understanding of the task.

Its very important that after you have completed your changes you perform the integration tests and make user that your changes work correctly on the different integration sites.

---

for posthog analytics create unique labels for each cta or any link
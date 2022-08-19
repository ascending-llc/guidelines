# ASCENDING AWS CloudFormation Creation Rule

Tagging is enforced for creating any CloudFormation Stack in ASCENDING AWS account. 

* When attempt to create a ClouldFormation stack, you must add at least one tag (key/value pair) by following the rule: 
    * Use **owner** as the key and your name as the value.
* If you failed to use **owner** as your key or forget to include it in your tag, your creation will be denied.

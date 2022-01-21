# oh-my-zsh-aws-plugin 

Forked from dvsa/zsh-aws-plugin to fix an OSX compatability issue.

The function to handle the prompt notication of MFA expiry fails on some newer OSX versions due to 
changes in the <i>date</i> command's supported flags, specifically the ability to interpret the UTC
extension on the end of the $AWS_MFA_EXPIRY variable.

The solution is to install gnu-utils:
	
	brew install gnu-utils

...and then to replace the initial date used on line 250:

      local expiry_ts=`date -j -f "%Y-%m-%dT%H:%M:%SZ" $AWS_MFA_EXPIRY +"%s"`

...with...

      local expiry_ts=`gdate -d $AWS_MFA_EXPIRY +%s`


# oh-my-zsh-aws-plugin 

Forked from dvsa/zsh-aws-plugin to fix an OSX compatability issue.

Requirements:
Need to install coreutils (for gdate) and gsed

The function to handle the prompt notication of MFA expiry fails on some newer OSX versions due to 
changes in the <i>date</i> command's supported flags, specifically the ability to interpret the UTC
extension on the end of the $AWS_MFA_EXPIRY variable.

The solution is to install coreutils:
	
	brew install coreutils

...and then to replace the initial date used on line 250:

      local expiry_ts=`date -j -f "%Y-%m-%dT%H:%M:%SZ" $AWS_MFA_EXPIRY +"%s"`

...with...

      local expiry_ts=`gdate -d $AWS_MFA_EXPIRY +%s`

Update 19/12/2022:

Changed the aws_auth_mfa to lookup the mfa_serial from within your .aws/config file.
To use aws_auth_mfa you *MUST* have mfa_serial defined
This is because AWS have now enabled multiple MFA devices to be registered against your account
The lookup that was previously performed will now fail if there are multiple's configured

To use this you must have gsed installed (brew install gnu-sed)

Previous code:
local mfa_serial="$(aws iam list-mfa-devices --query 'MFADevices[*].SerialNumber' --output text)";

New code: 
local mfa_serial=`gsed -nr "/^\[profile $AWS_PROFILE\]/ { :l /^mfa_serial[ ]*=/ { s/[^=]*=[ ]*//; p; q;}; n; b l;}" ~/.aws/config`

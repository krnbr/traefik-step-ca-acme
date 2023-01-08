## the following commands are for step-ca:-

I in particular was exploring two step-ca server running in RA mode on my local
- one backed by cloud based step-ca cert manager

step-ca --context=acme.cloud --token=<replace with the token that smallstep cert manager generated for you> --issuer-password-file <(echo replace this password also) --resolver="127.0.0.1:53"

update the acme settings for the cloud one:-

step ca provisioner update acme-ra-jwk-hidden --x509-min-dur=720h1m0s --x509-default-dur=4380h1m0s --x509-max-dur=9490h1m0s --context=acme.cloud --ca-url=https://hidden.ca.smallstep.com --root=/Volumes/user/path/root_ca.crt

No admin credentials found.
You must login to execute admin commands.

✔ Please enter admin name/subject (e.g., name@example.com): email id

✔ Provisioner: authority-admin (OIDC) [client: hidden] - choose oidc when asked - it will take us to browser based login and fill in the username and password accordingly

Your default web browser has been opened to visit:

- the other backed by self hosted one - hosted on my public VM

step ca init --ra=StepCAS --profile=self-managed context=self-managed

prompts should be like below:-

`user@MACHINE pwd % step ca init --ra=StepCAS --profile=self-managed context=self-managed`

`✔ Deployment Type: Standalone`

`What is the url of your CA?`

`✔ (e.g. https://ca.smallstep.com:9000): https://ca.domain.com:port - actual would vary person to person`

`What is the fingerprint of the CA's root file?`

`✔ (e.g. 4fe5f5ef09e95c803fdcb80b8cf511e2a885eb86f3ce74e3e90e62fa3faf1531): <hidden from the with actual one>`

`What is the JWK provisioner you want to use?`

`✔ (e.g. you@smallstep.com): admin - by default admin is default JWK`

`What DNS names or IP addresses would you like to add to your new CA?`

`✔ (e.g. ca.smallstep.com[,1.1.1.1,etc.]): localhost,127.0.0.1`

`What IP and port will your new CA bind to?`

`✔ (e.g. :443 or 127.0.0.1:443): :5321`

`What would you like to name the CA's first provisioner?`

`✔ (e.g. you@smallstep.com): admin`

`Choose a password for your first provisioner.`

`✔ [leave empty and we'll generate one]: `

`✔ Would you like to overwrite /Users/user/.step/authorities/local.neuw/certs/root_ca.crt [y/n]: y`

`✔ Root certificate: /Users/user/.step/authorities/local.neuw/certs/root_ca.crt`

`✔ Root fingerprint: d08445237e3efbc3e5b6b76a41f27ffcacd4a9999ded34682b3a9b321589f73f`

`✔ Would you like to overwrite /Users/user/.step/authorities/local.neuw/config/ca.json [y/n]: y`

`✔ Would you like to overwrite /Users/user/.step/authorities/local.neuw/config/defaults.json [y/n]: y`

`✔ Database folder: /Users/user/.step/authorities/local.neuw/db`

`✔ Default configuration: /Users/user/.step/authorities/local.neuw/config/defaults.json`

`✔ Default profile configuration: /Users/user/.step/profiles/self-managed/config/defaults.json`

`✔ Certificate Authority configuration: /Users/user/.step/authorities/local.neuw/config/ca.json`


IMPORTANT NOTE - ACME provisioner will exist on local RA but it will be backed by CA's JWK admin provisioner - the command is like below for that:-

`step ca provisioner add acme --type ACME --x509-min-dur=1h --x509-max-dur=9490h1m0s --x509-default-dur=9490h1m0s --context=local.neuw`
  
We will get below error, if the acme provisioner did not exist on the RA that be just created and started without configuring it

provisioner acme not found, traefik will also throw resource not found exception or something similar

step ca root root.crt --ca-url https://ca-url --fingerprint <fingerprint>
step certificate p12 karanbir.p12 karanbir.client.crt karanbir.client.key
step ca certificate --context=name "karanbir" karanbir.self.managed.crt karanbir.self.managed.ke
  
## running traefik 

Important - an env variable LEGO_CA_CERTIFICATES is required for adding trust to the root certs of the ACME server, this way traefik will be able to call the acme calls without any issues

CLUB multiple certs for multiple acme - if the case is like that. Though that would be rarest case for someone

export LEGO_CA_CERTIFICATES="/Users/user/.step/authorities/path/certs/root_ca.crt"

`./traefik --configfile=./traefik.yml`

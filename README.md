# ENCRYPT-PROPERTY-VALUE
Encrypt sensitive information, such as database password in Properties File

<p>We’ll use “jasypt — a java based encryption utility” to encrypt our password.</p>

<p>Jasypt (Java Simplified Encryption) Spring Boot provides utilities for encrypting property sources in Boot applications.
By using Jasypt, we can provide encryption for the property file attributes and our application will do the job of decrypting it and retrieving the original value.</p>

<strong>CONFIGURE POM.XML</strong>
<p>Add the below dependency</p>

<pre class="fn fo fp fq fr ht hu hv"><span id="d0d4" class="hw gp dc bk hx b eg hy hz r ia">&lt;dependency&gt;<br/> &lt;groupId&gt;com.github.ulisesbocchio&lt;/groupId&gt;<br/> &lt;artifactId&gt;jasypt-spring-boot-starter&lt;/artifactId&gt;<br/> &lt;version&gt;1.18&lt;/version&gt;<br/>&lt;/dependency&gt;</span></pre><p id="5e0b" class="ha hb dc bk hc b hd ho hf hp hh hq hj hr hl hs hn">

Once this is added a jar will be pulled into to your local mvn repository, which we’ll use to encrypt our db password.
JasyptPBEStringEncryptionCLI is the CLI interface to encrypt which takes the below arguments:
input : plain text you want to encrypt
password : a shared secret / passphrase you need to remember to encrypt/decrypt (in our case it makes more sense to have it specific to your deployment environment)
algorithm : encryption algorithm | allowed algorithms are — PBEWITHMD5ANDDES, PBEWITHMD5ANDTRIPLEDES, PBEWITHSHA1ANDDESEDE, PBEWITHSHA1ANDRC2_40
Now run the below command to encrypt the password:

<pre class="fn fo fp fq fr ht hu hv"><span id="3d74" class="hw gp dc bk hx b eg hy hz r ia">java -cp C:\Users\XXXX\.m2\repository\org\jasypt\jasypt\1.9.3\jasypt-1.9.3.jar org.jasypt.intf.cli.JasyptPBEStringEncryptionCLI input=root password=dev-env-secret algorithm=PBEWITHMD5ANDDES</span></pre>


if everything goes well you should see an output of this sort:</p><pre class="fn fo fp fq fr ht hu hv"><span id="a65f" class="hw gp dc bk hx b eg hy hz r ia">----ENVIRONMENT-----------------<br/> <br/>Runtime: Oracle Corporation Java HotSpot(TM) 64-Bit Server VM 25.181-b13<br/> <br/> <br/> <br/>----ARGUMENTS-------------------<br/> <br/>algorithm: PBEWITHMD5ANDDES<br/>input: root<br/>password: dev-env-secret<br/> <br/> <br/> <br/>----OUTPUT----------------------<br/> <br/>EHZOHWEHof1oCaywLdyFrg==</span></pre><p id="81c2" class="ha hb dc bk hc b hd ho hf hp hh hq hj hr hl hs hn">

Now take the OUTPUT in my case it is “EHZOHWEHof1oCaywLdyFrg==”.</p><p id="602e" class="ha hb dc bk hc b hd ho hf hp hh hq hj hr hl hs hn">Now you can place this encrypted string in your properties in the below format</p><p id="ed0d" class="ha hb dc bk hc b hd ho hf hp hh hq hj hr hl hs hn">ENC(***your encrypted password goes here***)</p><p id="e220" class="ha hb dc bk hc b hd ho hf hp hh hq hj hr hl hs hn">Here’s now the properties file will look like:</p><pre class="fn fo fp fq fr ht hu hv"><span id="f7ba" class="hw gp dc bk hx b eg hy hz r ia">spring.<br/>  datasource.<br/>    password= ENC(EHZOHWEHof1oCaywLdyFrg==)</span></pre><p id="998d" class="ha hb dc bk hc b hd ho hf hp hh hq hj hr hl hs hn">Springboot will decrypt automatically on boot-up when you execute your springboot application with the VM option “-Djasypt.encryptor.password=dev-env-secret”.</p><pre class="fn fo fp fq fr ht hu hv"><span id="68c3" class="hw gp dc bk hx b eg hy hz r ia">java -jar -Dapplication.properties=application.yaml -Djasypt.encryptor.password=ddev-env-secret springboot_app.jar</span></pre>

<p>Keep in mind, that you will need to start your application using the same password you used to encrypt the properties. So, you can start your app this way:</p>

<p>mvn -Djasypt.encryptor.password=dev-env-secret spring-boot:run</p>

<p><strong>for production environment,</strong> to avoid exposing the password in the command line, since you can query the processes with ps, previous commands with history, etc etc. You could:</p>

<p>Create a script like this: touch setEnv.sh
Edit setEnv.sh to export the JASYPT_ENCRYPTOR_PASSWORD variable
#!/bin/bash

export JASYPT_ENCRYPTOR_PASSWORD=supersecretz

Execute the file with . setEnv.sh
Run the app in background with mvn spring-boot:run &
Delete the file setEnv.sh
Unset the previous environment variable with: unset JASYPT_ENCRYPTOR_PASSWORD</p>

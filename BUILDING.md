# Instructions for building, testing, and deploying

## Building

Run maven as usual to get the dependencies and compile the code.

```bash
mvn clean compile
```

## Testing

```bash
mvn clean compile test
```

## Deploying/Releasing

Upon sucessful deployment, the package will be available on (Maven) [Central Repository](https://search.maven.org/).

We need to configure our local Maven (e.g., in `~./m2/settings.xml`) with the credentials for Central Repository and the gpg configuration (for signing the files)
Please make sure you have a gpg key beforehand (e.g., you may create one using `gpg --gen-key`).

```xml
  <profiles>

   <profile>
     <id>ossrh</id>
     <activation>
      <activeByDefault>true</activeByDefault>
     </activation>
     <properties>
       <gpg.keyname>your_gpg_keyname</gpg.keyname>
       <gpg.passphrase>your_gpg_password</gpg.passphrase>
     </properties>
   </profile>

  </profiles>

   ...
  <servers>
    
   <server>
    <id>ossrh</id>
    <username>your_username_on_sonatype_org</username>
    <password>your_password_on_sonatype_org</password>
   </server>

  </servers>
```

Your gpg keyname can be found with `gpg --list-signatures --keyid-format 0xshort`. More info [here](https://central.sonatype.org/publish/publish-maven/#gpg-signed-components).

Then build, run the tests, and deploy.

```bash
mvn clean compile test package javadoc:aggregate-jar deploy
```

Note that the `gpg:sign` goal should be called implicitly by the `package` goal.

If we want to manually validate the signed artifact before calling the `deploy` goal, we can use gpg tool:

```bash
$ gpg --verify target/xray-testng-extensions-0.1.0-SNAPSHOT.jar.asc

gpg: assuming signed data in 'target/xray-testng-extensions-0.1.0-SNAPSHOT.jar'
gpg: Signature made Sex  3 Jun 11:58:00 2022 WEST
gpg:                using EDDSA key A4FC49BB43A2C8B1BD6A34B395B6F2BD5378949F
gpg: Good signature from "Sergio Freire <sergio@example.com>" [ultimate]
```

The `deploy` will upload the artifact to (Maven) Central Repository, but won't create any tags or other assets.

Go to GH page of this project and create the release by hand, using the tag following semantic versioning.
Tag can be created at the moment of the creation of the release (easier) or it can be created locally and push to GH.

Example:

```bash
git tag -a "0.1.0" -m "v0.1.0"
git push origin --tags
```

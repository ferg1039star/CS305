Client and requirements

Artemis Financial is a financial consulting firm. Their web application handles sensitive client data and documents. They needed the connection encrypted in transit and a way to verify that data had not been altered. The deliverable was a Spring Boot application that serves content over HTTPS and returns a SHA-256 checksum of a unique string.

What went well

The useful part was separating real findings from noise. Dependency-Check flags every old transitive dependency. The refactor itself added no new libraries, so the post-change report matched the starter report. That confirmed the new code did not introduce vulnerabilities. Secure coding matters here because a financial app that leaks or alters client data creates regulatory, legal, and reputation damage that is harder to fix than the original bug. Security is not an add-on. It is what lets the company keep operating after the first incident.

Challenging and helpful parts

The challenging part was the project root. IntelliJ opened the inner folder, so Maven looked for src/src and the run configuration missed target/classes. Once the working directory was the folder that contains pom.xml, the rest was mechanical. The helpful part was the Dependency-Check HTML report. It made the “did I add anything new?” question a diff instead of a guess.

Layers of security

Three layers were added. A self-signed certificate from keytool (RSA-2048, SHA256withRSA). Spring Boot configuration that binds that keystore and listens on 8443 so Tomcat starts an SSL connector. A /hash endpoint that returns a SHA-256 digest of a string containing the developer’s name, so integrity can be checked. In the future I would keep Dependency-Check (or an equivalent SCA tool) in the build, add a secrets scanner, and treat the NVD/CISA KEV list as the priority queue for what to patch first.

Functional and secure after refactor

The app was started and https://localhost:8443/hash returned the expected name and a 64-character checksum. Dependency-Check was run again after the change. The vulnerable dependencies were the same ones already present in the starter (Spring Boot 2.2.4, Tomcat 9.0.30, SnakeYAML, and the rest). No new third-party vulnerability was introduced by the checksum or SSL configuration.

Tools and practices worth keeping

keytool for certificates, Spring Boot server.ssl.* properties for TLS, MessageDigest with SHA-256 for integrity, OWASP Dependency-Check for static analysis of dependencies, and the habit of confirming the Maven root before debugging anything else. Those transfer to any Java service.

What to show an employer

The running HTTPS endpoint with a verifiable SHA-256 checksum, the keytool-generated certificate and the explanation of why the browser still shows a warning, and the before/after Dependency-Check result that proves the change did not add vulnerable libraries. That set shows secure communications, integrity checking, and basic supply-chain awareness without claiming the starter’s old dependencies were fixed.

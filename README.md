# RISC-V projects summary
## RISC-V chips tapped out 

| Chip name     | CPU speed     | Bits  | CPU core  | Release time | Note |
| ------------- |:-------------:| -----:| ------------- |:-------------:| -----:|
|  SiFive's     |:-------1.5GHz:| -64-bit-:| ------------- |:-------------:| -----:|
|  U54-MC       |    
|  Coreplex     | 
*This note is based on "Mobile Trusted Computing", Proceedings of the IEEE, Vol. 102, No. 8, August 2014. Reference link is [here](http://ieeexplore.ieee.org/document/6856168/)*

Trusted Computing Base (TCB) consists of hardware and firmware components that need to be trusted unconditionally. They are trust anchors of the computing system.

## Platform Integrity: 
Integrity of platform code (OS) can be verified either during system boot or at device runtime. Prevent or detect usage of platform versions that have been modified without authorization.

* **Secure Boot**: device startup process is stopped if any modification of the launched platform components is detected. Common approach is to use code signing combined with making the beginning of the boot sequence immutable by storing it within the TCB during manufacturing. The processor must unconditionally start executing from this memory location. Boot code certificates contain hashes of booted code, signed with respect to a verification root, such as the device manufacturer public key stored on the device. Use boot code certificates to verify the integrity of the booted components. Validate the signature of the system component launched first (boot loader) and in turn verify the next component launched (OS kernel) and so on. Failure on any validation steps will abort the boot process. (a) immutable boot sequence, (b) verification root, (c) integrity-protected cryptographic mechanism.

* **Authenticated Boot**: started platform components are measured but not verified with respect to any reference values. All measurements are logged in integrity-protected volatile memory. Boot loader measures the first component launched which in turn measures the next one and so on. Recorded measurements represent the state of the platform components after boot, and can be used for local access control enforcement or remote attestation. 

Three trust anchors: (a) integrity-protected volatile memory, (b) cryptographic mechanism.

Boot time integrity protects the system during bootup, can not protect the system after it is booted. In runtime platform integrity verification, a trusted software component monitors the integrity of the platform code continuously and repairs modified components automatically if possible. Integrity of the monitors can be verified using the verification techniques described above.

## Secure Storage: 
Store data on the device to disallow unauthorized access by REE components. Confidential and integrity-protected device-specific key that can be accessed only by authorized code. The key is initialized during manufacturing and stored in a protected memory area on the processor chip preventing extraction of the key through physical attacks. 

Two trust anchors: (a) device-specific key, (b) cryptographic mechanisms.

## Isolated Execution:
The ability of running security-critical code outside the control of the untrusted REE. Isolated execution combined with secure storage constitutes a TEE, which allows security applications resist REE compromise. TEE is a subset of the mobile device hardware TCB, can be seen as a component of the TCB. The cryptographic keys never leave the TEE. Need support in isolated execution of arbitrary code for application-specific algorithms. Hardware configuration need to provide an interface (TEE entry) through which the executable code (trusted applications) can be loaded for execution using the protected volatile memory.

TEE code certificate (1) authorizes code execution within the TEE, (2) authorizes trusted applications to access the device key and other device resources such as confidential data and hardware interfaces. The access to the device key and other device resources may be controlled by measured and saved platform states during authenticated boot process.

TEE management layer (software or firmware component) provides runtime environment for trusted applications and enforce access control to protected resources. The integrity of the management layer must be verified either as part of the boot time/runtime platform integrity verification, or on demand when trusted applications are loaded for execution. 

Three anchors: (a) isolated memory (volatile or nonvolatile), (b) cryptographic mechanisms, (c) verification boot.

## Device Authentication:
An external service provider can use device authentication to verify the identity of the mobile device and TEE. The identity may include device manufacturer information that can imply compliance to external service provider requirements. An identity certificate that is signed with respect to the aforementioned verification root binds an assigned identity to the base identity. Examples of International Mobile Equipment Identifier (IMEI), link-layer identities such as Bluetooth and WiFi addresses.

## Attestation and Provisioning:
An externally verifiable statement about the software configuration running on a device is called remote attestation. Remote attestation allows an external service provider to verify that a device is running a compliant platform version. Remote attestation implementation provides statements signed with the certified device key over authenticated measurements (e.g., cryptographic hash digests) of the firmware and software components loaded at boot time. 

The process of securely sending secrets and code to TEE of the target device is called provisioning. Device certificates do not include user identities and thus provisioning user authentication must be implemented by other means. All cryptographic keys needed for secure storage, isolated execution, device authentication, attestation, and provisioning can be derived from the same device key. 

## TEE Architecture:
Isolation needed for TEE can be achieved via (a) separating security elements, (b) secure processor modes, and (c) virtualization. A TEE instance is a processing environment that is isolated from the REE device OS. Single TEE instance for TEE architectures based on dedicated security chips and processor modes. Multiple TEE instances can be achieved through virtualization and emerging processor architectures. TEE provides API to allow REE applications to execute trusted applications and to read and write data in TEE. Single instance TEE needs TEE management layer, while multiple instance TEE may not use a management layer. In terms of size and complexity, the management layer is likely to be significantly smaller than the REE device OS, thus its attack surface is smaller. 




layout: post
title: C# Exam 40-783 Managing Assemblies
---

# Assembly attributes

// Set version number for the assembly.
[assembly:AssemblyVersionAttribute("4.3.2.1")]

// Set culture as German.
[assembly:AssemblyCultureAttribute("de")]

AssemblyKeyFileAttribute : String value indicating the name of the file that contains either the public key (if using delay signing) or both the public and private keys

AssemblyKeyNameAttribute : Indicates the key container that contains the key pair passed as a parameter to the constructor of this attribute.

# Strong name

A strong name consists of the assembly's identity—its simple text name, version number, and culture information (if provided)—plus a public key and a digital signature. It is generated from an assembly file using the corresponding private key.

# Delay signing

1. Obtain the public key portion of the key pair from the organization that will do the eventual signing. Typically this key is in the form of an .snk file, which can be created using the Strong Name tool (Sn.exe).

2. Annotate the source code for the assembly with two custom attributes from System.Reflection:

    ```cs        
    [assembly:AssemblyKeyFileAttribute("myKey.snk")]
    [assembly:AssemblyDelaySignAttribute(true)]
    ```

3.  The compiler inserts the public key into the assembly manifest and reserves space in the PE file for the full strong name signature. The real public key must be stored while the assembly is built so that other assemblies that reference this assembly can obtain the key to store in their own          assembly reference.

4.  Later, usually just before shipping, you submit the assembly to your organization's signing authority for the actual strong name signing using the –R option with the Strong Name tool.

# Global Assembly Cache

If you intend to share an assembly among several applications, you can install it into the global assembly cache.

The global assembly cache stores assemblies specifically designated to be shared by several applications on the computer. 

An assembly must have a  **strong name**  to be installed in the global assembly cache.

There are several reasons why you might want to install an assembly into the global assembly cache:

- Shared location.

    Assemblies that should be used by applications can be put in the global assembly cache. For example, if all applications should use an assembly located in the global assembly cache, a version policy statement can be added to the Machine.config file that redirects references to the assembly.

- File security.

Because the global assembly cache is installed in the systemroot directory, it inherits that directory's ACL. It is recommended that only users with Administrator privileges be allowed to delete files from the global assembly cache.

-   Side-by-side versioning.

Multiple copies of assemblies with the same name but different version information can be maintained in the global assembly cache.

-   Additional search location.

The common language runtime checks the global assembly cache for an assembly that matches the assembly request before probing or using the codebase information in a configuration file. 


## How to installed on the GAC

-   Using Windows Installer.

-   Using the Global Assembly Cache tool (Gacutil.exe). 

## Finding assembly

1.  Application configuration files (Probing):  Add extra locations where the CLR should look the assembly, in subdirectories of the application

2.  Publisher policy files: Specify that if the CLR looks for a specific assembly, it should bind to the new version. It needs to be deployed on the GAC

3.  Machine configuiration (CodeBase): Specify a location for an assembly that is outside of the application´s directory like another computer
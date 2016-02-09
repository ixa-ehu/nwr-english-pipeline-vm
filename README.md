

# Introduction #

This is a virtual machine (VM) with all the required modules and NLP processors to run the English pipeline developed within the [Newsreader](http://www.newsreader-project.eu) project for event extraction. The pipeline expects a [NAF](https://github.com/newsreader/NAF) document as input (see for example `docs/input/example.naf`) and produces NAF documents with the annotations of the different linguistic processors.

# Pre-requisites #

The are some pre-requisites the host machine has to fulfil for running the VM inside it:

    - Linux operating system (any recent flavor would do it) 

    - In-kernel KVM virtualization capabilities. You can also determine if your system processor supports KVM by running the following command:

        % grep -E 'vmx|svm' /proc/cpuinfo

      if this command returns output, then your system supports KVM. You also have to verify that the KVM-related feature is enabled in the machine's BIOS.

    - 64 bit CPU (x86_64)

# Download the virtual machine #

You need two files for running the VM. 

    http://ixa2.si.ehu.es/newsreader_resources/newsreaderpublicvm_v3.img
    http://ixa2.si.ehu.es/newsreader_resources/newsreaderpublicvm_v3.xml

The first is the VM image with all the required modules installed. The second is an short XML document which is needed for running the VM.

# Running the VM #

## Preliminary steps ##

For running the VM, you need first to do some preliminary steps:

* Install the necessary software into the host machine. On Deban/Ubuntu machines this includes the following packages:

    - qemu-kvm
    - libvirt-bin
    - virt-manager

* Tweak the the XML file:
    - Put the absolute path to the IMG file above in the `file` attribute of `<source>` element (under `<disk>`, around line 22)
    - Currently the VM is configured to use 12Gb RAM. You can change this value by editing around line 4 (`<memory>` and `<currentMemory>` elements). 
    - If you experience problems running the VM, maybe you need to change the `<emulator>` element (around line 19) and put the correct path of the kvm emulator executable in your system.

## Running the VM ##

From the host machine, cd to where the IMG and XML documents are and run the following:

    % virsh create newsreaderpublicvm_v3.xml
    Domain newsreaderpublicvm_v3 created from newsreaderpublicvm_v3.xml

The machine should be running now. You can test this using the `virsh list` command:

    % virsh list
     Id    Name                           State
    ----------------------------------------------------
     2     newsreaderpublicvm_v3          running

Note: The VM needs some time to completely load all the modules and services, so please be patient until the login screen appears.

The IP address of the machine is fixed: **192.168.122.92**

Now you can connect to the VM.

## Connecting via ssh ##

As said before, the IP address of the VM is 192.168.122.92. Thus, being on the host computer, just ssh to the VM using this address and the `newsreader` user:

    % ssh newsreader@192.168.122.92

The password is **readNEWS89**

# Running the pipeline #

Inside the VM, you can run the NewsReader pipeline by executing the following command:

    $ pipeline/run_pipeline.sh docs/input/example.naf > docs/output/example.naf

If you want to analyze the debug messages of the NLP modules, run this other script instead:

    $ pipeline/run_pipeline_v3_debug.sh docs/input/example.naf > docs/output/example.naf

# List of files and directories in th VM #

    + README.md      this file
    + components/    the different NLP processors
    + pipeline/      scripts for running the pipeline
    + docs/          directory with documents

# Pipeline components #

The current version of the English pipeline executes the following NLP processors following this specific order:

    components/EHU-tok.v30
    components/EHU-topic.v30
    components/EHU-pos.v30
    components/EHU-wikify.v30
    components/EHU-nerc.v30
    components/EHU-parse.v30
    components/EHU-corefgraph.v30
    components/VUA-opinion-miner.v30
    components/EHU-ukb.v30
    components/VUA-ims-wsd.v30
    components/EHU-ned.v30
    components/VUA-popen-nedreranker.v30
    components/EHU-srl.v30
    components/VUA-srl-postprocess.v30
    components/FBK-time.v30
    components/VUA-eventcoref.v30
    components/FBK-temprel.v30
    components/FBK-causalrel.v30
    components/VUA-factuality.v30

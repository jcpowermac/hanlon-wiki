# Razor Slices

In Razor, a *Slice* represents a unit of functionality. There are a number of Slices that are automatically loaded into Razor when the server starts, and it is a relatively simple matter to determine what slices are available at any given time by simply running the *razor* command with no additional arguments. Doing so will produce output that looks something like the following output:

    root@ubuntu-server:~# razor
    
    ProjectRazor - v0.1.0.2
    EMC Corporation
    
    	Usage: 
    	project_razor [slice name] [command argument] [command argument]...
    	 Switches:
    		 --debug        : Enables printing proper Ruby stacktrace
    		 --verbose      : Enables verbose object printing
    		 --no-color-out : Disables console color. Useful for script wrapping.
    
    Loaded slices:
    	[config] [boot] [bmc] [tagrule] [tagmatcher] [system] 
    	[policy] [node] [model] [logviewer] [imagesvc] 
    	
    root@ubuntu-server:~# 

As you can see from that output, the current default set includes the following slices:

1. bmc &mdash; Used to interact with the Baseboard Management Controllers (or BMCs) of the nodes in the system
2. config &mdash; Used to view the current Razor configuration
3. node &mdash; Used to view node-related information (meta-data discovered, last checkin time, current state, associated tags, etc.)
4. imagesvc &mdash; Used to load images into Razor and to use those images during the (iPXE) boot process
5. tagrule &mdash; Used to define tag rules
6. tagmatcher &mdash; Used to define rules that will match tags to nodes
7. model &mdash; Used to define 
8. policy &mdash; Used to define policies that will map models to nodes based on tagrules
9. logviewer &mdash; Used to view the Razor server's current logfile
10. boot &mdash;
11. system &mdash;

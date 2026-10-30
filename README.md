# jnr-jenner-analytcs-macro-function-to-execute-datasteps-and-procs-at-macro-time
Jenner Analytics macro function to execute datasteps and procs at macro time
   Jenner Analytics macro function to execute datasteps and procs at macro time
    
    PROBLEM
    =======
    Keep only varables that contain name, weight and height
    in sashelp.class and compute bmi.
    There are simple ways to solve this problem.
    This is a template for more expansive macro functions.
    
    WHAT WE WANT
    Name   Height Weight  BMI
    Amir     61.2     95  17.83
    Bethany  63.8    105  18.13
    
    USAGE
    data class;
      set sashelp.class(obs=3 keep=name %getvars(prx='/eight/i'));
      bmi=703*weight/height**2;
    run;
    
    filename ft15f001 "c:/otojnr/dosubl.sas";
    parmcards4;
    %macro dosubl(arg);
      %local rc;
      %let rc = %qsysfunc(dosubl(&arg));
    %mend dosubl;
    ;;;;
    run;
    
    filename ft15f001 "c:/otojnr/getvars.sas";
    parmcards4;
    %macro getvars(
        libname=sashelp
       ,memname=class
       ,prx='/(?!)/' /*-- always 0 so turns off prx ---*/
     ) / des="Populate global VARLIST matching column names";
    
      %global varlist;
      %let varlist=;
    
      %dosubl('
      proc sql noprint;
        select name
          into :varlist separated by " "
        from sashelp.vcolumn
        where upcase(libname) = upcase("&libname")
          and upcase(memname) = upcase("&memname")
          and prxmatch(&prx, name) > 0
        order by varnum
      ;quit;
      ');
      &varlist
    %mend getvars;
    ;;;;
    run;
    
    data class;
      set sashelp.class(obs=3 keep=name %getvars(prx='/eight/i'));
      bmi=703*weight/height**2;
    run;
    
    proc print data=class;
    run;
    
    LIST: 11:05:33
    ==============
    
     Obs  Name     Height  Weight            bmi
    
       1  Amir       61.2      95  17.8310158486
       2  Bethany    63.8     105  18.1344031603
       3  Carlos     58.5      88  18.0769961283
    
    
    LOG:  11:06:02
    ==============
    
    NOTE: Copyright (c) 2026 Jenner Analytics Ltd., London, England.
    NOTE: Jenner v1.5.107 (build v1.5.107+ec04e8abda.20261027T055441Z)
          Licensed to Roger DeAngelis, Serial 3BF7B3E6.
    NOTE: DATA _null_
    
    autexec started.
    
    NOTE: DATA elapsed:
      wall  0.00 seconds
      cpu   0.00 seconds
    NOTE: Library WORKX assigned path=d:\wpswrkx.
    NOTE: DATA workx.class
    
    NOTE: Processing inline DATALINES (19 lines)
    
    NOTE: Read 19 rows from DATALINES.
    NOTE: The data set WORKX.CLASS has 19 observations and 5 variables.
    NOTE: DATA elapsed:
      wall  0.00 seconds
      cpu   0.00 seconds
    NOTE: DATA _null_
    
    LOG:  11:06:02
    NOTE: DATA _null_ completed. Output written to FILE PRINT
    NOTE: Option SASAUTOS changed to c:/otojnr.
    NOTE: DATA _null_
    
    NOTE: Reading from fileref c:/jnr/runsas_selection.sas (c:/jnr/runsas_selection.sas)
        1 data class;
        2   set sashelp.class(obs=3 keep=name %getvars(prx='/eight/i'));
        3   bmi=703*weight/height**2;
        4 run;
    
    NOTE: Read 4 rows from c:/jnr/runsas_selection.sas.
    NOTE: DATA elapsed:
      wall  0.00 seconds
      cpu   0.02 seconds
    NOTE: DATA _null_
    
    autoexec completed  11:06:02
    
    NOTE: DATA elapsed:
      wall  0.00 seconds
      cpu   0.00 seconds
    NOTE: PROC SQL
    
    NOTE: Macro variable VARLIST = Height Weight
    NOTE: PROC SQL statement used.
    NOTE: DATA work.class
    
    
    NOTE: Read 3 rows from sashelp.class.
    NOTE: The data set WORK.CLASS has 3 observations and 4 variables.
    NOTE: DATA elapsed:
      wall  0.00 seconds
      cpu   0.00 seconds

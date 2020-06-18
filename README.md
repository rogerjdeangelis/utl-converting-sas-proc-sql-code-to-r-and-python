# utl-converting-sas-proc-sql-code-to-r-and-python
Converting sas proc sql code to r and python  
    Converting sas proc sql code to r and python                                                                    
                                                                                                                    
    github                                                                                                          
    https://tinyurl.com/yczw7yz8                                                                                    
    https://github.com/rogerjdeangelis/utl-converting-sas-proc-sql-code-to-r-and-python                             
                                                                                                                    
    *                                                                                                               
     ___  __ _ ___                                                                                                  
    / __|/ _` / __|                                                                                                 
    \__ \ (_| \__ \                                                                                                 
    |___/\__,_|___/                                                                                                 
                                                                                                                    
    ;                                                                                                               
                                                                                                                    
    options validvarname=upcase;                                                                                    
    libname sd1 "d:/sd1";                                                                                           
                                                                                                                    
    data sd1.have;                                                                                                  
      set sashelp.class(obs=7);                                                                                     
    run;quit;                                                                                                       
                                                                                                                    
                                                                                                                    
    proc sql;                                                                                                       
       create                                                                                                       
          table want_sas as                                                                                         
       select                                                                                                       
          sex                                                                                                       
         ,mean(age)    as avgAge                                                                                    
         ,mean(height) as avgHgt                                                                                    
         ,mean(weight) as avgWgt                                                                                    
       from                                                                                                         
          sd1.have                                                                                                  
       group                                                                                                        
          by sex                                                                                                    
    ;quit;                                                                                                          
                                                                                                                    
                                                                                                                    
                                                                                                                    
    WORK.WANT_SAS total obs=2                                                                                       
                                                                                                                    
     SEX     AVGAGE     AVGHGT     AVGWGT                                                                           
                                                                                                                    
      F     12.0000    55.9750    74.0000                                                                           
      M     11.6667    57.9333    89.1667                                                                           
                                                                                                                    
                                                                                                                    
    *____                                                                                                           
    |  _ \                                                                                                          
    | |_) |                                                                                                         
    |  _ <                                                                                                          
    |_| \_\                                                                                                         
                                                                                                                    
    ;                                                                                                               
                                                                                                                    
    %utlfkil(d:/xpt/want_r.xpt);                                                                                    
                                                                                                                    
    proc datasets lib=work;                                                                                         
      delete want_r;                                                                                                
    run;quit;                                                                                                       
                                                                                                                    
    %utl_submit_r64('                                                                                               
    library(haven);                                                                                                 
    library(sqldf);                                                                                                 
    library(SASxport);                                                                                              
    have<-read_sas("d:/sd1/have.sas7bdat");                                                                         
    want_r <- sqldf("                                                                                               
         select                                                                                                     
            sex                                                                                                     
           ,avg(AGE)    as avgAge                                                                                   
           ,avg(HEIGHT) as avgHgt                                                                                   
           ,avg(WEIGHT) as avgWgt                                                                                   
         from                                                                                                       
            have                                                                                                    
         group                                                                                                      
            by sex                                                                                                  
         ");                                                                                                        
    want_r;                                                                                                         
    write.xport(want_r,file="d:/xpt/want_r.xpt");                                                                   
    ');                                                                                                             
                                                                                                                    
    libname xpt xport "d:/xpt/want_r.xpt";                                                                          
    data want_r;                                                                                                    
      set xpt.want_r;                                                                                               
    run;quit;                                                                                                       
    libname xpt clear;                                                                                              
                                                                                                                    
                                                                                                                    
                                                                                                                    
    WORK.WANT_R total obs=2                                                                                         
                                                                                                                    
     SEX     AVGAGE     AVGHGT     AVGWGT                                                                           
                                                                                                                    
      F     12.0000    55.9750    74.0000                                                                           
      M     11.6667    57.9333    89.1667                                                                           
                                                                                                                    
    *            _   _                                                                                              
     _ __  _   _| |_| |__   ___  _ __                                                                               
    | '_ \| | | | __| '_ \ / _ \| '_ \                                                                              
    | |_) | |_| | |_| | | | (_) | | | |                                                                             
    | .__/ \__, |\__|_| |_|\___/|_| |_|                                                                             
    |_|    |___/                                                                                                    
    ;                                                                                                               
                                                                                                                    
                                                                                                                    
    %utlfkil(d:/xpt/want_py.xpt);                                                                                   
                                                                                                                    
    proc datasets lib=work;                                                                                         
      delete want_py;                                                                                               
    run;quit;                                                                                                       
                                                                                                                    
    %utl_submit_py64_38('                                                                                           
    import pandas as pd;                                                                                            
    import pyreadstat;                                                                                              
    from pandasql import sqldf;                                                                                     
    have, meta = pyreadstat.read_sas7bdat("d:/sd1/have.sas7bdat");                                                  
    print(have);                                                                                                    
    pysqldf = lambda q: sqldf(q, globals());                                                                        
    want_py = """                                                                                                   
         select                                                                                                     
            sex                                                                                                     
           ,avg(AGE)    as avgAge                                                                                   
           ,avg(HEIGHT) as avgHgt                                                                                   
           ,avg(WEIGHT) as avgWgt                                                                                   
         from                                                                                                       
            have                                                                                                    
         group                                                                                                      
            by sex;                                                                                                 
         """;                                                                                                       
    want_py=pysqldf(want_py);                                                                                       
    want_py.info();                                                                                                 
    print(want_py);                                                                                                 
    pyreadstat.write_xport(want_py, "d:/xpt/want_py.xpt",table_name="want_py");                                     
    ');                                                                                                             
                                                                                                                    
    libname xpt xport "d:/xpt/want_py.xpt";                                                                         
    data want_py;                                                                                                   
      set xpt.want_py;                                                                                              
    run;quit;                                                                                                       
    libname xpt clear;                                                                                              
                                                                                                                    
    WORK.WANT_PY total obs=2                                                                                        
                                                                                                                    
      SEX     AVGAGE     AVGHGT     AVGWGT                                                                          
                                                                                                                    
       F     12.0000    55.9750    74.0000                                                                          
       M     11.6667    57.9333    89.1667                                                                          
                                                                                                                    
                                                                                                                    

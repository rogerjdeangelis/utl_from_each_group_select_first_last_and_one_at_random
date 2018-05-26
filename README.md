# utl_from_each_group_select_first_last_and_one_at_random
From each group select first last and one at random. Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.
    From each group select first last and one at random

    Same result in WPS and SAS

    From Paul Dorfman post on SAS-L

    github (use the sas code not readme for copy paste)
    https://tinyurl.com/yc8j99oz
    https://github.com/rogerjdeangelis/utl_from_each_group_select_first_last_and_one_at_random

    SAS Forum
    https://tinyurl.com/yckh69dm
    https://communities.sas.com/t5/Base-SAS-Programming/How-to-select-randomly-observation-from-each-groups/m-p/464988

    INPUT
    =====

    WORK.HAVE total obs=12
                        |
     ID       DATE      |  RULES
                        |
      1    04/05/2012   |  ** Keep this one
      1    05/07/2012   |  **
      1    05/09/2012   |  ** Pick a random date from these three
      1    16/07/2012   |  **
      1    07/12/2013   |  ** Keep this one

      2    05/08/2012   |  ** keep both
      2    12/10/2012   |

      3    01/05/2012   |  ** Keep
      3    06/05/2012   |
      3    08/06/2012   |
      3    12/10/2012   |
      3    16/11/2012   |
      3    01/01/2013   |  ** seed chooses this one
      3    01/03/2013   |
      3    01/05/2013   |
      3    01/06/2013   |
      3    01/07/2013   |
      3    01/08/2013   |  ** Keep


    EXAMPLE OUTPUT

     WORK.WANT total obs=8

       ID       DATE

        1    04/05/2012
        1    05/09/2012
        1    07/12/2013

        2    05/08/2012
        2    12/10/2012

        3    01/05/2012
        3    01/01/2013
        3    01/08/2013


    PROCESS
    =======

    data want (drop = K N) ;
      call streaminit(1477);
      do N = 1 by 1 until (last.id) ;
        set have;
        by id ;
      end ;
      K = 1 ;  * choose one between first and last;
      do _n_ = 1 to N ;
        set have ;
        by id;
        if first.id=0 and last.id=0 then do;
           * if by chance we are on last available prob is 1 to choose it;
           if rand ('uniform') < divide (K,N-2) then do ;
               output ;
               K +- 1;
           end ;
        end;
        else output;
        N +- 1 ;
      end ;
    run ; quit;


    OUTPUT
    ======

     WORK.WANT total obs=8

     ID       DATE

      1    04/05/2012
      1    05/09/2012
      1    07/12/2013
      2    05/08/2012
      2    12/10/2012
      3    01/05/2012
      3    01/01/2013
      3    01/08/2013

    *                _               _       _
     _ __ ___   __ _| | _____     __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \   / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/  | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|   \__,_|\__,_|\__\__,_|

    ;

    data have;
     input id date $10.;
    cards;
     1 04/05/2012
     1 05/07/2012
     1 05/09/2012
     1 16/07/2012
     1 07/12/2013
     2 05/08/2012
     2 12/10/2012
     3 01/05/2012
     3 06/05/2012
     3 08/06/2012
     3 12/10/2012
     3 16/11/2012
     3 01/01/2013
     3 01/03/2013
     3 01/05/2013
     3 01/06/2013
     3 01/07/2013
     3 01/08/2013
    ;;;;
    run;quit;


    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;

    see process for SAS

    * wps;

    %utl_submit_wps64('
    libname wrk sas7bdat "%sysfunc(pathname(work))";
    data wrk.wantwps (drop = K N) ;
      call streaminit(1477);
      do N = 1 by 1 until (last.id) ;
        set wrk.have;
        by id ;
      end ;
      K = 1 ;  * choose one between first and last;
      do _n_ = 1 to N ;
        set wrk.have ;
        by id;
        if first.id=0 and last.id=0 then do;
           * if by chance we are on last available prob is 1 to choose it;
           if rand ("uniform") < divide (K,N-2) then do ;
               output ;
               K +- 1;
           end ;
        end;
        else output;
        N +- 1 ;
      end ;
    run;quit;
    ');


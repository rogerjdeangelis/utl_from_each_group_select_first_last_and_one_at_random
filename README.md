# utl_from_each_group_select_first_last_and_one_at_random
From each group select first last and one at random. Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.
    From each group select first last and one at random

    Same result in WPS and SAS
    
    see Mark and Paul's New Solutions on end
    Paul Dorfman
    Mark Keintz

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


    *____             _
    |  _ \ __ _ _   _| |
    | |_) / _` | | | | |
    |  __/ (_| | |_| | |
    |_|   \__,_|\__,_|_|

    ;

    Best regards,
    Paul Dorfman

    Roger,

    Tis' nice. But would it not be a bit simpler to output the !st and Nth
    (or just the !st if the BY-group has only one), then pick just one
    randomly between 2 and N-1 after the DoW is over for the BY-group and then fetch it with point= ? Something like:

    data have;
      input id date ;
      cards;
    1  101
    2  201
    2  202
    3  301
    3  302
    3  303
    4  401
    4  402
    4  403
    4  404
    5  501
    5  502
    5  503
    5  504
    5  505
    ;
    run ;

    data want (drop = _:) ;
      do _n_ = 1 by 1 until (last.id) ;
        set have ;
        by ID ;
        _q ++ 1 ;
        if _n_ = 1 then output ; *output !st;
      end ;
      if _n_ > 1 ;
      output ;                         *output last if _n_>1;
      if _n_ > 2 ;
      p = _q - ceil (rand ("uniform") * (_n_ - 2)) ; *pick one if _n_=>3;
      set have point = p ;
      output ;
    run ;

    The drawback is that the random one will be out of order by date,
    if that matters. On the positive side, you won't have to read each BY-group twice.

    Mark,

    But indeed. It's a very interesting variation I haven't quite thought of!

    On the other hand, this problem example is a curious app for various
    features of the hash and hash iterator objects:

    data want ;
      dcl hash h (ordered:"A") ;
      dcl hiter i ("h") ;
      h.definekey ("_n_") ;
      h.definedata ("ID", "date") ;
      h.definedone () ;
      do _n_ = 1 by 1 until (last.id) ;
        set have ;
        by ID ;
        h.add() ;
      end ;
      if _n_ <= 3 then do while (i.next() = 0) ;
        output ;
      end ;
      else do ;
        i.first() ;                                               output ;
        i.setcur (key: ceil (rand ("uniform") * (_n_ - 2)) + 1) ; output ;
        i.last() ;                                                output ;
      end ;
      h.delete() ;
      i.delete() ;
    run ;

    Note that i.delete() is not imperative; but I don't want to spawn another iterator
    instance for each BY-group while keeping all those having already been spawned.


    *__  __            _
    |  \/  | __ _ _ __| | __
    | |\/| |/ _` | '__| |/ /
    | |  | | (_| | |  |   <
    |_|  |_|\__,_|_|  |_|\_\

    ;

    Mark Keintz
    mkeintz@wharton.upenn.edu

    If other conditions require populating the hash with all observations, then I like the ability to
      else do rc=i.first(),i.setcur (key:ceil(rand("uniform")*(_n_-2))+1),i.last() ;
        output ;
      end ;

    especially  because the SETCUR key can be an expression (unlike POINT= in the SET statement).

    But if all the non-selected observations have no other utility, why make the hash
    object larger than necessary?  Particularly if ID groups are large?  My first
    inclination would be to keep only the selected observations in the hash object,
    then do a bulk output at END_OF_HAVE:

    data _null_;
      if _n_=1 then do;
        dcl hash h (ordered:"A") ;
          h.definekey ("_n_") ;
          h.definedata ("ID", "date") ;
          h.definedone () ;
      end;
      set have end=end_of_have;
      by id;

      retain maxrand middle_n;

      if first.id then do;
        h.add();
        middle_n=_n_+0.5;
        maxrand=.;
      end;
      else if last.id then rc=h.add();
      else do;
        thisrand=rand('uniform');
        _n_=middle_n;
        if thisrand>maxrand then rc=h.replace();
        maxrand=max(maxrand,thisrand);
      end;

      if end_of_have then h.output(dataset:'want');
    run;

    regards,
    Mark





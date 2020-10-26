Using ``fatigue`` with ``chronic`` and ``cron``
===============================================

Cron sends email if there is any output, regardless of exit status.  Notably,
it does not send email if the command exits with failure silently. [#silent-failure]_

``fatigue`` modifies exit codes (which alone have no effect on cron outcome).

``fatigue --quiet`` modifies exits codes and output.

``chronic`` modifies output.

The decision table below describes the notification behaviour of a cronjob run
with ``fatigue`` and/or ``chronic``.

====================  ===========  =========  =  ===========  =========  =  ===================
                  Original                    →          Modified        =  Outcome
--------------------------------------------  -  ----------------------  -  -------------------
Command               Output       Exit code  →  Output       Exit code  =  cron
====================  ===========  =========  =  ===========  =========  =  ===================
…                     no           success    →  ·            ·          =  …is silent
…                     no           failure    →  ·            ·          =  …is silent
…                     yes, new     success    →  ·            ·          =  …sends email
…                     yes, new     failure    →  ·            ·          =  …sends email
…                     yes, repeat  success    →  ·            ·          =  …sends email
…                     yes, repeat  failure    →  ·            ·          =  …sends email

fatigue …             no           success    →  ·            ·          =  …is silent
fatigue …             no           failure    →  ·            ·          =  …is silent
fatigue …             yes, new     success    →  ·            ·          =  …sends email
fatigue …             yes, new     failure    →  ·            ·          =  …sends email
fatigue …             yes, repeat  success    →  ·            ·          =  …sends email
fatigue …             yes, repeat  failure    →  ·            success    =  …sends email

fatigue --quiet …     no           success    →  ·            ·          =  …is silent
fatigue --quiet …     no           failure    →  ·            ·          =  …is silent
fatigue --quiet …     yes, new     success    →  ·            ·          =  …sends email
fatigue --quiet …     yes, new     failure    →  ·            ·          =  …sends email
fatigue --quiet …     yes, repeat  success    →  ·            ·          =  …sends email
fatigue --quiet …     yes, repeat  failure    →  no           success    =  …is silent

chronic …             no           success    →  ·            ·          =  …is silent
chronic …             no           failure    →  ·            ·          =  …is silent
chronic …             yes, new     success    →  no           ·          =  …is silent
chronic …             yes, new     failure    →  ·            ·          =  …sends email
chronic …             yes, repeat  success    →  no           ·          =  …is silent
chronic …             yes, repeat  failure    →  ·            ·          =  …sends email

chronic fatigue …     no           success    →  ·            ·          =  …is silent
chronic fatigue …     no           failure    →  ·            ·          =  …is silent
chronic fatigue …     yes, new     success    →  no           ·          =  …is silent
chronic fatigue …     yes, new     failure    →  ·            ·          =  …sends email
chronic fatigue …     yes, repeat  success    →  no           ·          =  …is silent
chronic fatigue …     yes, repeat  failure    →  no           success    =  …is silent

chronic -e …          no           success    →  ·            ·          =  …is silent
chronic -e …          no           failure    →  ·            ·          =  …is silent
chronic -e …          yes, new     success    →  ·            failure    =  …sends email
chronic -e …          yes, new     failure    →  ·            ·          =  …sends email
chronic -e …          yes, repeat  success    →  ·            failure    =  …sends email
chronic -e …          yes, repeat  failure    →  ·            ·          =  …sends email

fatigue chronic -e …  no           success    →  ·            ·          =  …is silent
fatigue chronic -e …  no           failure    →  ·            ·          =  …is silent
fatigue chronic -e …  yes, new     success    →  ·            failure    =  …sends email
fatigue chronic -e …  yes, new     failure    →  ·            ·          =  …sends email
fatigue chronic -e …  yes, repeat  success    →  ·            .          =  …is silent
fatigue chronic -e …  yes, repeat  failure    →  ·            ·          =  …is silent
====================  ===========  =========  =  ===========  =========  =  ===================


.. [#silent-failure]
  You can use ``chronic -v …`` to handle that case, or your own command wrapper
  which prints some output if the command fails.

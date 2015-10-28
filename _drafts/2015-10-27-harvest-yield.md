
## Notes on recovering from failure

Notes from the Brewer paper about building scalable internet systems [0], relating uptime and mean-time-between-failure (MTBF) and mean-time-to-repair (MTTR):

uptime = (MTBF - MTTR)/MTBF

> you can improve uptime either by reducing the frequency of failures, or reducing the time to fix them. Although the former is more pleasing aesthetically, the latter is much easier to accomplish with evolving systems.

In other words, it's just as important to assume failures and improve tooling around debugging and recovery as it is to program defensively and attempt to prevent failures in the first place.

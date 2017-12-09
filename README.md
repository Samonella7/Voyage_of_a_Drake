This is an add-on for The Battle For Wesnoth (www.wesnoth.org). It is currently unfinished (and therefore unpublished)
but an older version was made availabe at https://forums.wesnoth.org/viewtopic.php?f=8&t=45215

Credits listed in about.cfg

Please comment at the forum linked above.

This is an RPG campaign, unique even among other RPG campaigns. Rather than focusing on weapons, spells, etc,
like most Wesnoth RPGs do, it focuses on story and interactions with the NPCs. 

Below is an overview of the code organization. Note: This is not written to work with github's .md formatting; click "raw" above to make it readable.

1) Events that specifically apply to one person are defined in that unit's unit file. 
        or example, almost every person has one large "new_schedule" event that defines how
        that unit moves around depending on the day/night. More details can be found in
        utils/scenario_schedule_utils.cfg, which basically drives the day/night schedules.

2) When moving units (as part of their "new_schedule"s or otherwise) there are 
        a few important things to know:
    a) if they enter or leave an area (areas being defined in point (6)), they must be teleported
        manually (via TELEPORT_UNIT). The tools that teleport the protaganist in and out of doors work
        on him alone, for very good reasons.
    b) A useful movement tool is defined and explained in utils/macros/movement_macros.cfg
        seriously, it's explained. there are probably almost as many comments in
        that file as the rest of the campaign.
    
3) Each person has a container variable specific to his/her name. This container might hold
        any number of specific values. For example, whenever Vyncent is in the family cart, his 
        normal unit is stored in $Vyncent.unit|
    a) Groups of people can also have a dedicated container variable too. For example, 
        $gladiators.last_stargazer| names the last elf that went stargazing (because the two of
        them alternate)
    b) One important variable that the individual peoples' container variables almost always hold 
        is called $conversation|. This holds a string value identical to the name of the event that
        should be fired if Voadar talks to that unit. This way, the "new_schedule" events mentioned in
        point number (1) can easily control what people have to say during any part of their schedule. 
        NOTE: naming events in $conversation| only means something if the unit's file includes the 
        {VOADAR_CREATE_CONVERSATION_OPTION UNIT_ID} macro, defined in utils/voadar_macros.cfg.
        It is that macro that creates the right-click menu option for talking to {UNIT_ID}, and selecting
        said menu-option is what fires the event named by $<id_of_that_unit>.conversation|
    
4) Naming conventions: These conventions are supposed to make it easier to remember variable/event names,
        but I'm not certain that I always stick to them. 
    Anyway: character's names are always capitalized, whether it be 
        in an event name, a variable name, or a unit ID. This is actually important because many 
        macros rely on the unit's id and container variable name being identical. 
        Note: only the first letter is capitalized, even if there are two parts in the name: $Mrs_aristo|
    Also: words besides names are never capitalized. For example, $gladiators| from point (3) 
        is not capitalized.
    Finally: almost all events are given an id, even if they don't necessarily need one. If the
        event has a custom name (as opposed to something like "new turn") the name and id 
        generally should match (but not if there are multiple events with that same custom name, of course).
    Oh wait one more: every macro I defined begins with VOADAR (example in point (3b)).
        That way it should be easy to tell which macros I defined and which are part of mainline wesnoth.
        
5) Areas: different areas (for example, inside a house versus in the village versus in the forest)
    Basically all the rules are defined and explained in utils/area_events_general.cfg, 
    utils/area_events_tutorial.cfg, and utils/area_events_scenario.cfg
    
6) Voadar mechanics: inventory, talking, etc
    All this stuff is defined and explained in utils/voadar_events.cfg
    
7) Keyword bolding
    Defined and explained in utils/monument_options.cfg
    Basically, I want to bold keywords to help player's figure out what to do, but
    I want them to be able to turn that off, too. So instead of putting <b></b> in
    the messages, I put $d|$b|, and those variables should either be empty or contain 
    <b> and </b> respectively. Therefore, by changing the values of $d| and $b|, I can
    let the player control whether keywords are bolded or not.
    I hope this won't cause trouble with translations... but it probably will. Luckily, 
    there's no reason to think anyone will want to translate this anyway.
    
8) Oh also: for some reason poison hasn't been working (maybe because unhealable=yes? idk)
    Anyway, the current work around is an event in 2_Scenario.cfg that manually harms/animates 
    any unit that's poisoned after their turn refresh.

9) Economy: This information doesn't really belong here, but it doesn't really belong anywhere
    else. This is just a record of how much various goods cost, to act as a reference when i/you
    add something else having to do with money:
        HP sales:
    50hp worth of meat from Grigar:             5 gold      10  hp/gold
    25hp worth of meat from the tavern:         4 gold      6.25hp/gold
    3hp worth of ale from the tavern:           1 gold      3   hp/gold
    10hp, 1xp of meditation from Robert:        1 gold      10  hp/gold (eventually: fire resistance improvement)
    12hp+cure of fish from Juditha:             4 gold      3   hp/gold (plus cure)
    12hp+cure of fish from Rachael:             3 gold      4   hp/gold (plus cure)
    fullhp sleep from inn:                      15 gold     2-3 hp/gold (first time, earmuffs)
    full+cure cheater potion from Klippy:       20 gold     2ishhp/gold (always usable)
        Other sales:
    net (fishing and attack/story)              15 gold
    bunny race betting                          1,10,30 gold
    spectate gladiator match                    1 gold
    sword (unplanned reward)                    20 gold
    armor (+15% blade & pierce resistances)     25 gold
    padding (+10% cold, +10% impact)            15 gold
    warmth charm (+30% cold, +10% arcane)       40 gold
    armor upgrade (+20% impact +30% other phys) 60 gold
    manicure (+2 melee damage)                  30 gold
    
        Income/payments:
    bunny race betting                          1,10,30 gold
    deliver a letter                            3 gold
    carapace                                    5 gold
    cut wood                                    1,4 gold
    gladiator win                               4,7,12,50 gold
    selling bear claws                          10,15,20,40 gold, or 1 fish, or 2 meat
    stolen goods                                7,15 gold
	charm										25 gold
	herbs to tourists							4 gold for 1/3 of bag
    
13)Basic util structure: so up to this point I've mentioned a lot of utils/whatever.cfg files. 
    I guess it's time I explain a bit about that. 
    So there are three util directors, and I'll explain them in reverse order of importance 
    (though they're all important)
   first: utils/editor/
        This one has definitions for custom terrain types. Almost all of them are just embellishments,
        things like tables or chests that normally i'd use an [item] tag for, but there are enough that
        i much prefer to place and organize them in the map-editor.
   second: utils/macros/
        All the files contain only macro definitions, so these files shouldn't be included directly from
        a scenario; just use the macros defined in them (since the whole directory is included from _main)
        There are three major types of files: 
      a) unsorted_macros.cfg is just what it sounds like; if you see me use some macro that I haven't mentioned here 
        and isn't defined in the same file it's used, there's a
        good chance it's from unsorted_macros.cfg. 
      b) scenario_micro_ais.cfg, which was mentioned earier in (2b).
      c) all the other files; these correspond directly to some file from utils/, and i'll explain more in that 
        section (directly below)
   third: utils/
        These files should contain nothing but events. They could be dumped straight into the scenario/unit they're 
        used in (in fact, they are, by the preprocessor) but they're pulled out for a purely organizational purpose. Because 
        these could be included in multiple scenarios, they should define no macros that they don't locally undefine. 
        But sometimes macros are needed as an "interface" for other files to this one. That's why (3)all the other files in utils/macros 
        correspond to one of the files here. A utils/ file and its sister utils/macros/ file should be thought of as one file, 
        just spilt to control when they are preprocessed (not unlike .hpp and .cpp files in C++)





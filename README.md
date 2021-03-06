# MapTool-1.3.b89-CCG
Mod to the Open-Source MapTool application to add features.
-----------------------------------------------------------
MapTool Card Game Edition with Sound is an unofficial modification of MapTool version 1.3B89.
Although it is geared towards campaigns that implement card games and board games as opposed to
traditional roleplaying campaigns, it benefits can also be applied to other implementation
such a traditional campaigns.

1.0 Features:

A brief description of modifications and features is listed below with details in following
sections:

* Streaming and caching sound support. A number of new macro functions have been added for
  initiating streaming sounds for a sound repository. The implementation supports features such
  a caching sound files for quicker re-use, setting if players or GM only can initiate sounds,
  applying ear plugs to avoid sounds, providing visual cues for audio when ear plugs are on,
  pre-caching sounds so that larger sounds files are ready when they need to be, multi channel
  support so that audio files can be played while background music is playing and so on.
  There is even support for indicating on the server side that specific sounds files are not
  to be cached using a method that does not require any special code on the server side.

* A Cache Cleaner has been added which monitors and optionally cleans the assetcache directory.
  If the size of the directory exceeds a predetermined size, optional warnings can be displayed.
  If the size of the directory exceeds a second predetermined value, automatic cache cleaning
  can be initiated which removes cached assests based on their last access time. Meaning old
  not longer used assets are deleted first. Automatic cleaning continues until the total size
  of the assetcache directory drops below a third predetermined value. Custom settings allow
  various levels of information to be presented to the user from describing each step of the
  Cache Cleaner process to automatic cleaning with no visible infromatio to the user.
  Cache Cleaner is typically not needed in the unmodified MapTool because image assets tend to
  be small and thus do not take up much space. However, Cache Cleaner was added because of the
  added sound functionality. Since sounds have the option of being cached, the assetcache directory
  could fill up faster (depending on how much the sound features are used).

* Common Macros can now be run from a token's right button context menu. In the unmodified
  version of MapTool (1.3b89) when a single token is selected and right clicked the context
  menu that appears includes a macro directory from which any of the token's macros can be
  executed. When multiple tokens were selected, the macro directory was not available because
  not all token's may have the same macros available. This check was removed from this modified
  version allowing a token's right click content menu to be used to run a macro on multiple
  selected tokens. For now the selection is based on the token that was used to bring up the
  context menu but the macro will be executed on all selected tokens if they have that macro.

* Accessibility to Token Macros in the right click context menu has been improved by moving
  them from a macro sub-folder to the main conext level. This mean when a token is right
  clicked, the available macros are accessible immediately without having to do to a sub-folder.
  Along the same lines, many of the usual options in the right click context menu have been
  removed or made only visible to the GM to provide a focus on the token macro and less focus
  on other less used options. None of these options have actually been removed from MapTool,
  they have just been removed from the right context menu and thus are still accessible via
  macros. This feature makes it much easier to play any campaign which relies heavily on
  token macros. Instead of having to have the Selected window open and selecting macro there
  or navigating to the macros sub-folder on the context menu, the macros are all available
  as soon as the token is right clicked.

* The setOwner macro function has been fixed to support true Owned By All. According to the
  documentation for the unmodified MapTool 1.3b89, to set a token so that it is owned by all
  the setOwner macro function is used with the getAllPlayerNames. However, this has the effect
  of adding each player to the token's ownership instead of selecting the All Players checkbox.
  This has two problems: 1) when new players join they don't automatically have ownership of
  the token and 2) the isOwnedByAll macro function returns false (instead of the expected true).
  The modified setOwner macro function supports an "All" reserved word which selects the
  All Players checkbox but, for better backwards compatability, also check if the onwer list,
  passed to the function, is the complete player list. If it is, it assumes that the All Players
  selection is desired and checks the All Players checkbox instead of adding each player to the
  token's ownership list.


2.0 Requirements:

Due to the sound functionality which uses Java 7, the unofficial MapTool modification requires
Java 7 (or higher) to run. It will not run on clients using Java 6 (or earlier).

The host and all client must be using this modified version for compatability. The version
of this maptool has been coded as 1.3b89_CGEwS thus ensuring that clients must have the
same modified version in order to connect to the server. This modified version is fully
backwards compatible with unmodified MapTool version 1.3b89 campaign files but it may be
necessary to open them up in this modified version of MapTool first and re-save them.


3.0 Sound Functionality:

Although the sounds functionality provided is typically triggered using macros (see below)
it is distributed and triggered using Chat Messages visible in the Chat Window. This means
that the functionality not only triggers local sounds but also corresponding sounds on
connected clients.

Sounds are provided using a sound repository which is NOT included as part of this MapTool
modification. If hosting sounds files for a LAN campaign which does not have access to the
internet the a streaming capable webserver can be set up such as IIS or XAMPP Portable Edition.
Each of these takes a bit to set up but the process is generally not too difficult. However,
the setting up of such a webhosting service is beyond the scope of this document. If hosting
a game over the internet, the above solution can be used or a cloud based solution can be
used such as Dropbox. At least one MapTool user has tried this and report success streaming
sounds, using this MapTool modification, from Dropbox.

Unless the server file specified no caching (see below) the first time a sound file is
requested it is streamed from the sound server but it is also cached locally in the MapTool
assetcache directory so that repeated requests to play the sound file will execute faster
(and do not need access to the sound server). This does mean, however, that especially the
first time a sound file is requested to play there will be a delay between the request and
the playing of the file. This delay will depend on the user's internet connection, the
sound server's transfer rate, and the size of the sound file. It is highly recommended that
large sound files (e.g. background music) be pre-cached before they are used. This also
means that use of these sound functions should not rely on all clients playing the sound
file at the same time because this is unlikely to happen. Depending on the factors listed
previously, some clients may play the requested sound before others.

Sound functionality is threaded so that it does not block the use of MapTool while audio
files are being streamed, are playing or are being cached.

The following sounds related macro function have been added:

playSound(AudioFile) - Used to play a sounds from the assetcache or, if not cached, from the
                       currently configured audio repository. The sounds is played on the
                       local client machine plus is requested to be played by all connected
                       clients. The only parameter to this function is the name of the audio
                       file to be played without the audio repository prefix or the extension
                       (WAV) suffix. Uses the current set channel (see below) for playing.
                       Causes functionality locally and remotely.

                       Example: playSound("Hello")

playSoundLocal(AudioFile) - Same as playSound except that the sound is only played locally
                            but is not requested to be played on connected clients.
                            Uses the current set channel (see below) for playing.
                            Causes functionality only locally.

                            Example: playLocalSound("Hello")
				
stopSound(Method) - Stops the currently playing audio (if any) on the currently selected channel.
		    The only parameter to this function is an integer (0 to 1) which determines
                    if the stop is abrupt (i.e. immediate even if in mid play) or just prevents
                    the next repetition (if any).
                    Causes functionality locally and remotely.

		    Example: playStop(1)

loopSound(Repetitions) - Determines the number of repetitions that all future playSound and
                         playSoundLocal macro will play (until the value is changed again)
                         for the currently selected channel. Each channel has its own repetition
                         settings and thus the repetitions for one channel can be changed without
                         affecting the other channel. Does not affect audio already playing.
                         Causes functionality only locally.

			 Example: loopSound(3)
	    
setChannel(Channel) - Determine which channel, of 2, all future playSound and playSoundLocal
                      macro will use. Two channels are provided so that it is possible to play
		      background music or sound effects while playings other audio such as speech.
                      The only numeric parameter is channel number 1 or 2.
                      Causes functionality only locally.

lockChannel(Setting) - Determines if players can make playSound and playSoundLocal request using
                       this channel or if the channel can only be used by GM requests. The single
                       parameter to this function is a integer (0 or 1) which all players can use
                       the channel when the value is when 0 and only GM can use this channel when
                       the value is 1 (i.e. locked). Please note that when a channel is locked to
                       GM only, the channel will still be used on all client but only the GM will
                       be able to initiate playSound and playSoundLocal request to that channel.
                       When the channel is locked and a player tries to use it, an error message
                       is displayed indicating that the channel is locked.
                       Causes functionality locally and remotely.
                       
earPlugs(Setting) - Allows a player to ignore all playSound and playSoundLocal requests. Any such
                    request will still be shown in the Chat Windows for player reference but no
                    audio will be played. It should be noted that earPlugs only apply to audio
                    generated by playSound and playSoundLocal it will not affect the audio that
                    is generated in MapTool through other means (i.e. sounds made by the unmodified
                    version of MapTool). As setting of 0 means ear plugs not is use (i.e. audio
                    will be played) while a value of 1 means ear plugs are in use.
                    Causes functionality only locally.

cacheSound(AudioFile) - This function pre-loads a sound file from the sound repository to the
                        assetcache directory (assuming it does not have a no-cache flag on the
                        server). This is typically used to pre-load larger audio file before they
                        are needed so that when they are requested using the playSound macro they
                        are available locally and thus don't need to be streamed from the audio
                        server. This function can also be used to load all audio files, used in
                        the campaign, to the local clients so that the sound repository is no
                        longer needed. The audio file parameter follows the same rules as the
                        playSound function.
                        Causes functionality locally and remotely.

audioRepository(Path) - Overrides the currently set audio repository location. This function is
                        typically used when sound files reside on multiple audio servers. A macro
                        can first use this macro function to set the sound repository location
                        and then execute the desired playSound macro function. The default value
                        is read from the Repository.Audio.txt file in the MapTool config directory.


Note: setChannel and loopSound are listed as "Causes functionality locally and remotely". Obviously
      once the playSound macro function is used their effects will be felt remotely but the macro
      functions themselves are local only. This means when one client set a channel or set the
      number of repetitions, it does not affect the channel selection or repetitions setting on
      other clients.

3.1 No-Cache Feature

The No-Cache feature is not intended to be an enforcement mechanism to prevent users from stealing
copyrighted sound files. It is intended as a request mechanism by which the server can request
MapTool not to cache a sound file and this modification of MapTool (if not modified further by
users) will honor that request and not cache the file.

The No-Cache mechanism is initiated on the sound server side but does not need any special code
on the server side to support it. To mark a sound file on the server as anti-cache (typically
because the sound file is licenses for multi user play but not for multi user distribution) the
file name is renamed to include ".no-cache" before the WAV extension. For example, the audio file
"Hello.Wav" would become "Hello.no-cache.wav".

When a playSound request is made, MapTool first tried to play the requested audio file from the
local assetcache. If not present it will try to play the cachable version from the audio repository
and cache it locally for later re-use. If a cachable version is not found on the audio repository,
MapTool inserts the no-cache flag into the audio file request and tried again. If the no-cache
version is found, it is played but not cached.

Obviously if a sound file is not cached it will have a delay before it plays each time the file
is requested whereas if the sound file is cached it will only have a significant delay the first
time the file is used.

3.2 Audio Repository Default Configuration

The configuration of the Audio Repository is not currently available from the GUI, it must be
done manually before using the audio enabled campaign. To setup the default Audio Repository
(i.e. the Audio Repository location that is used if the audioRepository() macro function is not
used to change it), create a text file using any text based editor (such as Notepad). In the
text file, enter the base location of the audio repository. This should be the part of the URL,
needed to stream/download the sound file, before the name of the audio file. For example, if
the URL to access the sound file Hello.wav is:

http://sounds.freesound.com/Sounds/Hello.wav

Then the corresponding Audio Repository entry would be:

http://sounds.freesound.com/Sounds/

Note that the entry should include the trailing slash.

Save the text file using the name "Repository.Audio.Txt" in the MapTool config directory.
(On Windows machines this is typically in C:\Users\Username\.maptool\config)

When a playSound or playSoundLocal request is made, the contents of the Audio Repository entry
is appended with the requested audio file and the extension .WAV to build the complete URL to
the audio file. Please note that at this moment only WAV files are supported and they need to
be PCM encoded (see Java 7 Clip function for more specific details). Some audio files that I
have downloaded from the internet where in this format and some where not. If your audio file
does not play or produces odd sounding audio, try downloading a free audio converter (like Audacity)
and re-save the audio file as 16 bit PCM WAV file. Hopefully MP3 support will come soon.


4.0 Cache Cleaner

Because the playSound macro function cache sounds in the assetcache directory this directory could
fill up much faster than would normally be expected in the unmodified version of MapTool (in which
case the assetcache was only used for images). Probably even with sounds beinf cached this is not
likely to happen unless a user uses many campaigns with lots of audio files but there could be some
user running this modified MapTool on a client with limited resources in which case it could be
useful to keep an eye on the assetcache directory size and perform some cleanup when necessary.

This is where Cache Cleaner comes in. Cache Cleaner can be configured with three different properties
that determine when to act and how much. These properties are:

CacheWarning
CacheCutoff
CacheClean

Each of these can be assigned a value in MB and triggers possible actions in Cache Cleaner when
the assetcache directory exceeds the given amount.

CacheWarning - When the assetcache directory size exceeds CacheWarning, MapTools provides the user
               with a warning message either through a warning popup or a message in the Chat Window.
               No automatic cache cleaning is activated at this time.
               This option can be supressed using the Quiet mode (see below).

CacheCutoff - When the assetcache directory size exceds CacheCutoff, MapTools provides the user with
              a warning either through a warning popup or a message in the Chat Windows and automatically
              activates Cache Cleaning (see below). The warning message can be supressed using Quiet
              mode (see below) and the automatic cleaning can be supressed using X mode (see below).

CacheClean - When automatic cleaning is initiated Cache Cleaner keeps deleting assets (see below for
             details how assets are chosen) until the assetcache drops below this value. Typically
             the CacheClean value is set below the CacheWarning so that after cleaning and restarting
             MapTools, the cache warning does not immediately show up again.

4.1 Cache Cleaner Modes

In addition to these 3 properties Cache Cleaner takes a mode string which determines which features
Cache Cleaner should use. The following options are available. The mode string can contain multiple
options but some options obviously contradict each other and thus should not be used together.
Available modes are:

C = Chat Window (will display cache information, warnings and cutoff messages to the Chat Windows
    prefixed by "Cache:")
W = Warning Message (will display warnings and cutoff messages only using the warning popup message box)
I = Information Message (will display cache information, even if there is not warning, using an information
    popup message box)
Q = Quiet (will not provide any cache info messages but will still perform automatic cleanup when necessary)
X = Does not perform automatic cleanup

Examples of combined options mode:

QX = Basically turns Cache Cleaner off because it does not display any cache warnings and does not
     automatically activate cleaning
CX = Provide cache information, via chat window, including warnings and cutoff messages but does not
     automatically clean. Used when user wants to handle cleaning manually.
WX = Provide cache warnings and cutoff messages, via warning popup message box, but does not automatically
     clean. Used when user wants to handle cleaning manually.

4.2 Cache Cleaner Cleaning Logic

When automatic cleaning is initiated, MapTool starts off with a cutoff date that is roughly one year in the
past. It then checks each file's Last Access Time. If any of the file's Last Access Time is older than this
date they are deleted. It then decreases the cutoff date by a day a repeats the process. It continues to
do this until either it has deleted enough assetcache files to bring the directory size below the CacheClean
value or it reaches the current date. If it reaches the current date without having deleted enough files,
it means that some files in the assetcache directory must be write protected which is not normal. In such
a case, a warning message is displayed and the user should unprotect these files in order to allow Cache
Cleaner to perform its duty.

Cache Clean checks are perform on MapTool start and are threaded so that cache cleaning, if necessary, does
not prevent MapTool form being use since Cache Cleaning can take some time.

4.3 Cache Cleaner Configuration

Like the Audio Repository configuration (see above) the Cache Cleaner configuration is not available through
the GUI. If Cache Cleaner is not configured through a configuration text file, it assumes default values of
1024, 2048 and 512 (all values are in MB) and a mode of W. To configure Cache Cleaner with different values
create a text file using any text based editor (such as Notepad) with the file name "Cache.Txt" in the
MapTool config directory. (Typically found in C:\Users\UserName\.maptool\config on Windows machines).
The contents of the file should be 4 lines. Comments can be added after the 4 lines but not before. No empty
lines should be inserted before or between the 4 first lines. The contents of these line should be:

CacheWarning
CacheCutOff
CacheClean
Mode

The first three lines are all values in MB without the MB suffix. The last line can be any combination of
mode characters that make sense (see above). For example, to keep the same Warning, Cutoff and Clean values
but change the notification from the default warning popup (W) to Char Window (C) the contents of the file
would be:

1024
2048
512
C


5.0 Common Macros From Context Menu

When multiple tokens are selected and the right click context menu is brought up, the macro selection now
remains available (unlike the unmodified MapTool version 1.3b89). This allows a macro that is on the active
token (the one that was right clicked to get the context menu) to be executed on all selected tokens.

Please be advised that as of this version there is no check to ensure that the selected macro exists on
all of the selected tokens. If one or more selected tokens does not contain the selected macro then a
Chat Window error message will be generated and the macro may not get executed on all selected tokens
that have the macro. This is a known issue (and was, is my understanding, why the unmodified version of
MapTool removed the macro functionality from the context menu when multiple tokens are selected) but
since it does not cause a failure of the campaign and, from my experience, occurs very infrequently
(i.e. the user has to select an incompatible token) that it was not worth it to hold up releasing the
first version of this modified MapTool release.

The intention of this modification is to allow all macro function to be executed from the token right
click context menu as opposed to having to use the Campaign or Selected windows. By not having to use
the Campaign or Selected windows, more screen is available for the campaign and token actions can be
performed a bit faster since the user does not need to navigate to the Campaign or Selected windows to
activate macros.


6.0 Macros At Root Of Token Context Menu

Although this modified MapTool can be used for many applications, it was geared towards Card Game and
Board Game campaign implementations. In such cases it is highly desirable to have quick access to the
token's macros because most of the actions (such as turning over cards, putting cards into a player's hand,
shuffling cards, and so on) are done though these macros. On the other hand, many of the other MapTool
features such as Overlays, Changinf Facing, Healthbars, etc are either not use at all by the campaigns
or are not used directly by the player. For example, overlays are used but they are applied through
macros instead of the player opening the right context menu and selecting the overlay. As such the
less frequently used features where removed from the token's right context menu and the macros where
moved from their usual macro sub-folder to the root of the context menu (seperated into groups like
they are on the Selected window). This means that when a player right clicks a token they get instant
access to the token's macros as opposed to having to navigate to sub-folder.

It should be noted that all of the functionality that was removed from the token right click context
menu was not actually removed from MapTool it was just removed from the context menu. Thus any of the
removed functions can still be used through token macros.


7.0 SetOwner Fixed

This last modification will be of more interest to campaign designers and less to players. The current
setOwner function, in the unmodified MapTool version 1.3b89, does not have good support to tokens that
are owned by all players. When the Edit option is used on a token, and the ownership tab is selected
there is a checkbox for All Players and then a list which can contain player names. Typically a token
starts with the All Players checkbox checked and the player list empty. When in this state, the token
can be manipulated by any player and the isOwnedByAll function returns true.

However, when the ownership of a token changes to one or more players, the All Player checkbox becomes
deselected and the specific player's names are added to the ownership list. At this stage the macro
function isOwnedByAll returns false as is expected.

The problem occurs when the campaign wants to return a token's ownership to all players. There is no
selection for All Players in the setOwner macro function. Instead the Wiki documentation says to use
setOwner with the getAllPlayerNames macro function. However this is NOT the same as setting ownership
to all players. All this does is set each player's name in the ownership of the corresponding token.
Well if it lists all players then isn't that the same as owned by all? No. There are two significant
differences. First the isOwnedByAll macro function will still return false even though one would expect
the function to return true. This is because the All Player checkbox is not check. The second difference
is that if a player joins after a token has been setOwner to the result of getAllPlayerNames then the
new player will not be in the token's ownership list (unless the setOwner function is re-run).

To resolve the problem two modifications where made. For campaigns that are strictly for use with this
modified version of MapTool only (i.e. because, for example, they make use of playSound) the setOwner
macro function can use the reserved name "All". This will remove all player names from the token's
ownership list and, instead, check the All Players box. This will allow all players to manipulate the
token (even if they join after the setOwner function was used) and it will cause he isOwnedByAllm macro
function to, once again, return true.

However, for backwards compatability with unmodified MapTool version 1.3b89 campaign files, a second
modification was made when the function compares the provided owners list to the current players list.
If the owner list, provided to the setOwner function, matches the entire list of connected players then
the function assumes that the setOwner function is requesting All Players. In such a case, it takes the
same action as the "All" case discussed above.

In some cases the campaign may want to setOwner to all connected players but not actually want to set
the All Players (i.e. intentionally wanting players that connect later not to be in the ownership list).
In such a case there are a few work arounds to prevent the new modification from tripping the All Player
logic. When the setOwner compares the given owner list to the connected player list it does a simple
string comparison of the entire list. This means that if the order is different or the owner list
contains anything else, the strings will not match and thus the All Player logic will not be tripped.
Thus one of the simplest ways to prevent the All Player logic from tripping is to add a dummy name to
the end of the desired player list. For example, normally to set ownership of a token to all connected
players the macro function would be:

[h: setOwner(getAllPlayerNames("json"))]

This would be recognized by the modified setOwner macro function as All Players. If that was not desired
then code such as this could be used:

[h: setOwner(getAllPlayerNames("json")+",dummy")]

By adding a dummy name (could have been anything) to the player list, the provided list will not match
the connected list when the modified setOwner function compares them and thus the All Player logic will
not trip.

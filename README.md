# advanced charging controller (acc)


---
## description

acc is an android software mainly intended for [extending battery service life](https://batteryuniversity.com/learn/article/how_to_prolong_lithium_based_batteries).
in a nutshell, this is achieved through limiting charging current, temperature and voltage.
any root solution is supported.
the installation is always "systemless", whether or not the system is rooted with magisk.


---
## license

copyright 2017-2020, vr25

this program is free software: you can redistribute it and/or modify
it under the terms of the gnu general public license as published by
the free software foundation, either version 3 of the license, or
(at your option) any later version.

this program is distributed in the hope that it will be useful,
but without any warranty; without even the implied warranty of
merchantability or fitness for a particular purpose.  see the
gnu general public license for more details.

you should have received a copy of the gnu general public license
along with this program. if not, see <https://www.gnu.org/licenses/>.


---
## disclaimer

always read/reread this reference prior to installing/upgrading this software.

while no cats have been harmed, the author assumes no responsibility for anything that might break due to the use/misuse of it.

to prevent fraud, do not mirror any link associated with this project; do not share builds (zips/tarballs)! share official links instead.


---
## warning

acc manipulates android low level ([kernel](https://duckduckgo.com/lite/?q=kernel+android)) parameters which control the charging circuitry.
the author assumes no responsibility under anything that might break due to the use/misuse of this software.
by choosing to use/misuse it, you agree to do so at your own risk!


---
## prerequisites

- [must read - how to prolong lithium ion batteries lifespan](https://batteryuniversity.com/index.php/learn/article/how_to_prolong_lithium_based_batteries/)
- android or android based os
- any root method (e.g., [magisk](https://github.com/topjohnwu/magisk/))
- [busybox\*](https://github.com/search?o=desc&q=busybox+android&s=updated&type=repositories/) (only if not rooted with magisk)
- [curl](https://github.com/search?o=desc&q=curl+android&s=updated&type=repositories/) (for fbind --upgrade, optional)
- non-magisk users can enable acc auto-start by running /data/adb/$id/service.sh, a copy of, or a link to it - with init.d or an app that emulates it
- terminal emulator
- text editor (optional)

\* a busybox binary can simply be placed in /data/adb/bin/.
permissions (0700) are set automatically, as needed.
precedence: /data/adb/bin/busybox > magisk's busybox > system's busybox

other executables or static binaries can also be placed in /data/adb/bin/ (with proper permissions) instead of being installed system-wide.


---
## quick start guide


0. all commands/actions require root.

1. install/upgrade: flash\* the zip or use a front-end app (e.g. acca).
there are two additional ways of upgrading: `acc --upgrade` (online) and `acc --flash` (zip flasher).
rebooting after installation/removal is generally unnecessary.

2. [optional] run `acc` (wizard). that's the only command you need to remember.

3. [optional] run `acc pause_capacity resume_capacity` (default `75 70`) to set the battery levels at which charging should pause and resume, respectively.

4. if you come across any issues, refer to the `troubleshooting`, `tips` and `faq` sections below.
read as much as you can prior to reporting issues and/or asking questions.
oftentimes, solutions/answers will be right before your eyes.


### notes

steps `2` and `3` are optional because there are default settings.
for details, refer to the `default configuration` section below.
users are encouraged to try step `2` - to familiarize themselves with the available options.

settings can be overwhelming. start with what you understand.
the default configuration has you covered.
don't ever feel like you have to configure everything. you probably shouldn't anyway - unless you really know what you're doing.

uninstall: run `acc --uninstall` or flash\* `/sdcard/download/acc/acc-uninstaller.zip`.

acc runs in some recovery environments as well.
unless the zip is flashed again, manual initialization is required.
the initialization command is `/data/adb/acc/service.sh`.


---
## building and/or installing from source


### dependencies (build)

- git, wget, or curl (pick one)
- zip


### build tarballs and flashable zips

1. download and extract the source code: `git clone https://github.com/vr-25/acc.git`
or `wget  https://github.com/vr-25/acc/archive/master.tar.gz -o - | tar -xz`
or `curl -l#  https://github.com/vr-25/acc/archive/master.tar.gz | tar -xz`

2. `cd acc*`

3. `sh build.sh` (or double-click `build.bat` on windows 10, if you have windows subsystem for linux (with zip) installed)


#### notes

- build.sh automatically sets/corrects `id=*` in `*.sh` and `update-binary` files.
refer to framework-details.txt for a full list of tasks carried out by it.
to skip generating archives, run the build script with a random argument (e.g. bash build.sh h).

- the output files are (in `_builds/acc-$versioncode/`): `acc-$versioncode.zip`, `acc-$versioncode.tar.gz`, and `install-tarball.sh`.

- to update the local source code, run `git pull --force` or re-download it (with wget/curl) as described above.


### install from local sources or github

- `sh install-tarball.sh acc` installs the tarball (acc*gz) from the script's location.
the archive must be obtained from github: https://github.com/vr-25/acc/archive/$reference.tar.gz ($reference examples: master, dev, v2020.5.20-rc).

- `sh install.sh` installs acc from the extracted source.

- `sh install-online.sh [-c|--changelog] [-f|--force] [-k|--insecure] [-n|--non-interactive] [%install dir%] [reference]` downloads and installs acc from github. e.g., `sh install-online.sh dev`


#### notes

- `install.sh` and `install-tarball.sh` accept a custom _parent installation directory_ (e.g., `export installdir=/data; sh install.sh /data` - this will install acc in /data/acc/).

- in addition to the above, `install-online.sh` also recognizes a custom parent installation directory supplied as follows: `sh install-online.sh %path%` (e.g., `sh install-online.sh %/data%`).

- `install-online.sh` is the `acc --upgrade` back-end.

- the order of arguments doesn't matter.

- the default parent installation directories, in order of priority, are: `/data/data/mattecarra.accapp/files/` (acc app, `/data/adb/modules/` (magisk) and `/data/adb/` (other root solutions).

- no argument/option is strictly mandatory.
the exception is `--non-interactive` for front-end apps.
unofficially supported front-ends must specify the parent installation directory.
otherwise, the installer will follow the order above.

- the `--force` option to `install-online.sh` is meant for re-installation and downgrading.

- `sh install-online.sh --changelog --non-interactive` prints the version code (integer) and changelog url (string) when an update is available.
in interactive mode, it also asks the user whether they want to download and install the update.

- you may also want to read `setup/usage > terminal commands > exit codes` below.


---
## default configuration
```
#dc#

configvercode=202007220
capacity=(-1 60 70 75 false)
temperature=(40 60 90)
cooldownratio=()
cooldowncustom=()
resetbattstats=(false false)
loopdelay=(10 15)
chargingswitch=()
applyonboot=()
applyonplug=()
maxchargingcurrent=()
maxchargingvoltage=()
rebootonpause=
switchdelay=1.5
language=en
wakeunlock=()
prioritizebattidlemode=true
forcechargingstatusfullat100=
runcmdonpause=()
autoshutdownalertcmd=(vibrate 5 0.1)
chargdisablednotifcmd=(vibrate 3 0.1)
chargenablednotifcmd=(vibrate 4 0.1)
erroralertcmd=(vibrate 6 0.1)
ampfactor=
voltfactor=
ctrlfilewrites=(3 0.3)
loopcmd=()


# warnings

# as seen above, whatever is null can be null.
# nullifying values that should not be null causes nasty errors.
# however, doing so with "--set var=" restores the default value of "var".
# in other words, for regular users, "--set" is safer than modifying the config file directly.

# do not feel like you must configure everything!
# if you don't know exactly how to and why you want to do it, it's a very dumb idea.
# help is always available, from multiple sources - plus, you don't have to pay a penny for it.


# basic config explanation

# capacity=(shutdown_capacity cooldown_capacity resume_capacity pause_capacity capacity_freeze2)

# temperature=(cooldown_temp max_temp max_temp_pause)

# cooldownratio=(cooldown_charge cooldown_pause)

# cooldowncustom=cooldown_custom=(file raw_value charge_seconds pause_seconds)

# resetbattstats=(reset_batt_stats_on_pause reset_batt_stats_on_unplug)

# loopdelay=(loop_delay_charging loop_delay_discharging)

# chargingswitch=charging_switch=(ctrl_file1 on off ctrl_file2 on off --)

# applyonboot=apply_on_boot=(ctrl_file1::value1::default1 ctrl_file2::value2::default2 ... --exit)

# applyonplug=apply_on_plug=(ctrl_file1::value1::default1 ctrl_file2::value2::default2 ...)

# maxchargingcurrent=max_charging_current=([value] ctrl_file1::value::default1 ctrl_file2::value::default2 ...)

# maxchargingvoltage=max_charging_voltage=([value] ctrl_file1::value::default1 ctrl_file2::value::default2 ...) --exit)

# rebootonpause=reboot_on_pause=seconds

# switchdelay=switch_delay=seconds

# language=lang=language_code

# wakeunlock=wake_unlock=(wakelock1 wakelock2 ...)

# prioritizebattidlemode=prioritize_batt_idle_mode=true/false

# forcechargingstatusfullat100=force_charging_status_full_at_100=status_code

# runcmdonpause=run_cmd_on_pause=(. script)

# autoshutdownalertcmd=auto_shutdown_alert_cmd=(. script)

# chargdisablednotifcmd=charg_disabled_notif_cmd=(. script)

# chargenablednotifcmd=charg_enabled_notif_cmd=(. script)

# erroralertcmd=error_alert_cmd=(. script)

# ampfactor=amp_factor=[multiplier]

# voltfactor=volt_factor=[multiplier]

# ctrlfilewrites=ctrl_file_writes=(times interval)

# loopcmd=loop_cmd=(. script)


# variable aliases/sortcuts

# cc cooldown_capacity
# rc resume_capacity
# pc pause_capacity
# cft capacity_freeze2

# sc shutdown_capacity
# ct cooldown_temp
# cch cooldown_charge
# cp cooldown_pause

# mt max_temp
# mtp max_temp_pause

# ccu cooldown_custom

# rbsp reset_batt_stats_on_pause
# rbsu reset_batt_stats_on_unplug

# ldc loop_delay_charging
# ldd loop_delay_discharging

# s charging_switch

# ab apply_on_boot
# ap apply_on_plug

# mcc max_charging_current
# mcv max_charging_voltage

# rp reboot_on_pause
# sd switch_delay
# l lang
# wu wake_unlock
# pbim prioritize_batt_idle_mode
# ff force_charging_status_full_at_100
# rcp run_cmd_on_pause

# asac auto_shutdown_alert_cmd
# cdnc charg_disabled_notif_cmd
# cenc charg_enabled_notif_cmd
# eac error_alert_cmd

# af amp_factor
# vf volt_factor

# cfw ctrl_file_writes
# lc loop_cmd


# command examples

# acc 85 80
# acc -s pc=85 rc=80
# acc --set pause_capacity=85 resume_capacity=80

# acc -s "s=battery/charging_enabled 1 0"
# acc --set "charging_switch=/proc/mtk_battery_cmd/current_cmd 0::0 0::1 /proc/mtk_battery_cmd/en_power_path 1 0" ("::" = " ")

# acc -s sd=5
# acc -s switch_delay=5

# acc -s -v 3920 (millivolts)
# acc -s -c 500 (milliamps)

# custom config path
# acc /data/acc-night-config.txt 45 43
# acc /data/acc-night-config.txt -s c 500
# accd /data/acc-night-config.txt

# acc -s "ccu=battery/current_now 1450000 100 20"
# acc -s "cooldown_custom=battery/current_now 1450000 100 20"
# acc -s ccu="/sys/devices/virtual/thermal/thermal_zone1/temp 55 50 10"

# acc -s amp_factor=1000
# acc -s volt_factor=1000000

# acc -s mcc=500 mcv="3920 --exit"

# acc -s cfw="9 0.1"

# acc -s loop_cmd="echo 0 \\> battery/input_suspend"


# fine, but what does each of these variables actually mean?

# configvercode #
# this is checked during updates to determine whether config should be patched. do not modify.

# shutdown_capacity (sc) #
# when the battery is discharging and its capacity <= sc, acc daemon turns the phone off to reduce the discharge rate and protect the battery from potential damage induced by voltages below the operating range.
# on capacity <= shutdown_capacity + 5, accd enables android battery saver, triggers 5 vibrations once - and again on each subsequent capacity drop.

# cooldown_capacity (cc) #
# capacity at which the cooldown cycle starts.
# cooldown reduces battery stress induced by prolonged exposure to high temperature and high charging voltage.
# it does so through periodically pausing charging for a few seconds (more details below).

# resume_capacity (rc) #
# capacity at which charging should resume.

# pause_capacity (pc) #
# capacity at which charging should pause.

# capacity_freeze2 (cft) #
# this prevents android from getting capacity readings below 2%.
# it's useful on systems that shutdown before the battery is actually empty.

# cooldown_temp (ct) #
# temperature (°c) at which the cooldown cycle starts.
# cooldown reduces the battery degradation rate by lowering the device's temperature.
# refer back to cooldown_capacity for more details.

# max_temp (mt) #
# mtp or max_temp_pause #
# these two work together and are not tied to the cooldown cycle.
# on max_temp (°c), charging is paused for max_temp_pause (seconds).
# unlike the cooldown cycle, which aims at reducing both high temperature and high voltage induced stress - this is only meant specifically for reducing high temperature induced stress.
# even though both are separate features, this complements the cooldown cycle when environmental temperatures are off the charts.

# cooldown_charge (cch) #
# cooldown_pause (cp) #
# these two dictate the cooldown cycle intervals (seconds).
# when not set, the cycle is disabled.
# suggested values are cch=50 and cp=10.
# if charging gets a bit slower than desired, try cch=50 and cp=5.
# note that cooldown_capacity and cooldown_temp can be disabled individually by assigning them values that would never be reached under normal circumstances.

# cooldown_custom (ccu) #
# when cooldown_capacity and/or cooldown_temp don't suit your needs, this comes to the rescue.
# it takes precedence over the regular cooldown settings.
# refer back the command examples.

# reset_batt_stats_on_pause (rbsp) #
# reset battery stats after pausing charging.

# reset_batt_stats_on_unplug (rbsu) #
# reset battery stats after unplugging the charger and loop_delay_discharging (seconds) have passed.
# if the charger is replugged within loop_delay_discharging (seconds) after unplugging it, the operation is aborted.

# loop_delay_charging (ldc) #
# loop_delay_discharging (ldd) #
# these are delays (seconds) between loop iterations.
# lower values translate to quicker acc responsiveness - but at the cost of slightly extra cpu time.
# don't touch these (particularly ldd), unless you know exactly what you're doing.

# charging_switch (s) #
# if unset, acc cycles through its database and sets the first working switch/group that disables charging.
# if the set switch/group doesn't work, acc unsets chargingswitch and repeats the above.
# if all switches fail to disable charging, chargingswitch is unset, switchdelay is reverted to 1.5 and acc/d exit with error code 7.
# this automated process can be disabled by appending "--" to "charging_switch=...".
# e.g., acc -s s="battery/charge_enabled 1 0 --"

# apply_on_boot (ab) #
# settings to apply on boot or daemon start/restart.
# the --exit flag (refer back to applyonboot=...) tells the daemon to stop after applying settings.
# if the --exit flag is not included, default values are restored when the daemon stops.

# apply_on_plug (ap) #
# settings to apply on plug
# this exists because some /sys files (e.g., current_max) are reset on charger re-plug.
# default values are restored on unplug and when the daemon stops.

# max_charging_current (mcc) #
# max_charging_voltage (mcv) #
# only the current/voltage value is to be supplied.
# control files are automatically selected.
# refer back to the command examples.

# reboot_on_pause (rp) #
# if this doesn't make sense to you, you probably don't need it.
# essentially, this is a timeout (seconds) before rebooting - after pausing charging.
# this reboot is a workaround for a firmware issue that causes abnormally fast battery drain after charging is paused on certain devices.
# the issue has reportedly been fixed by the oems. this feature will eventually be removed.

# switch_delay (sd) #
# this is a delay (seconds) between charging status checks after toggling charging switches. it exists because some switches don't react immediately after being toggled.
# most devices/switches work with a value of 1.
# some may require a delay as high as 3. the optimal max is probably 3.5.
# if a charging switch seems to work intermittently, or fails completely, increasing this value may fix the issue.
# you absolutely should increase this value if "acc -t" reports total failure.
# some mediatek devices require a delay as high as 15!
# accd manages this setting dynamically.

# lang (l) #
# acc language, managed with "acc --set --lang" (acc -s l).

# wake_unlock (wu) #
# this is an attempt to release wakelocks acquired after disabling charging.
# it's totally experimental and may or may not work (expect side effects).

# prioritize_batt_idle_mode (pbim) #
# several devices can draw power directly from the external power supply when charging is paused. test yours with "acc -t".
# this setting dictates whether charging switches that support such feature should take precedence.

# force_charging_status_full_at_100 (ff) #
# some pixel devices were found to never report "full" status after the battery capacity reaches 100%.
# this setting forces android to behave as intended.
# for pixel devices, the status code of "full" is 5 (ff=5).
# the status code is found through trial and error, with the commands "dumpsys battery", "dumpsys battery set status #" and "dumpsys battery reset".

# run_cmd_on_pause (rcp) #
# run commands* after pausing charging.
# * usually a script ("sh some_file" or ". some_file")

# auto_shutdown_alert_cmd (asac) #
# charg_disabled_notif_cmd (cdnc) #
# charg_enabled_notif_cmd (cenc) #
# error_alert_cmd (eac) #
# as the names suggest, these properties dictate commands acc/d/s should run at each event.
# the default command is "vibrate <number of vibrations> <interval (seconds)>"
# termux apis can be used for notifications, tts, toasts and more. for details, refer to https://wiki.termux.com/wiki/termux:api .

# amp_factor (af) #
# volt_factor (vf) #
# unit multiplier for conversion (e.g., 1v = 1000000 microvolts)
# acc can automatically determine the units, but the mechanism is not 100% foolproof.
# e.g., if the input current is too low, the unit is miscalculated.
# this issue is rare, though.
# leave these properties alone if everything is running fine.

# ctrl_file_writes (cfw) #
# this determines how many times each ctrl file should be written to, as well as the interval (seconds) between these writes.
# that's an attempt to force changes that don't take effect immediately or at all.

# loop_cmd (lc) #
# this is meant for extending accd's functionality.
# it is periodically executed by is_charging() - which is called regularly, within the main accd loop.
# the boolean ischarging is available.
# refer back to command examples.

#/dc#
```

---
## setup/usage


as the default configuration (above) suggests, acc is designed to run out of the box, with little to no customization/intervention.

the only command you have to remember is `acc`.
it's a wizard you'll either love or hate.

if you feel uncomfortable with the command line, skip this section and use the [acc app](https://github.com/mattecarra/acca/releases/) to manage acc.

alternatively, you can use a `text editor` to modify `/data/adb/acc-data/config.txt`.
restart the [daemon](https://en.wikipedia.org/wiki/daemon_(computing)) afterwards, by running `accd`.
the config file itself has configuration instructions.
these instructions are the same found in the `default config` section, above.


### terminal commands
```
#tc#

usage

  acc   wizard

  accd   start/restart accd

  accd.   stop acc/daemon

  accd,   print acc/daemon status (running or not)

  acc [pause_capacity resume_capacity]   e.g., acc 75 70

  acc [options] [args]   refer to the list of options below

  acca [options] [args]   acc optimized for front-ends

  a custom config path can be specified as first parameter.
  if the file doesn't exist, the current config is cloned.
    e.g.,
      acc /data/acc-night-config.txt --set pause_capacity=45 resume_capacity=43
      acc /data/acc-night-config.txt --set --current 500
      accd /data/acc-night-config.txt


options

  -c|--config [editor] [editor_opts]   edit config (default editor: nano/vim/vi)
    e.g.,
      acc -c (edit w/ nano/vim/vi)
      acc -c less
      acc -c cat

  -d|--disable [#%, #s, #m or #h (optional)]   disable charging
    e.g.,
      acc -d 70% (do not recharge until capacity <= 70%)
      acc -d 1h (do not recharge until 1 hour has passed)

  -d|--daemon   print daemon status, (and if running) version and pid
    e.g., acc -d (alias: "accd,")

  -d|--daemon [start|stop|restart]   manage daemon
    e.g.,
      acc -d start (alias: accd)
      acc -d restart (alias: accd)
      accd -d stop (alias: "accd.")

  -e|--enable [#%, #s, #m or #h (optional)]   enable charging
    e.g.,
      acc -e 75% (recharge to 75%)
      acc -e 30m (recharge for 30 minutes)

  -f|--force|--full [capacity]   charge once to a given capacity (default: 100%), without restrictions
    e.g.,
      acc -f 95 (charge to 95%)
      acc -f (charge to 100%)
    note: if the desired % is less than pause_capacity, use acc -e #%

  -f|--flash ["zip_file"]   flash any zip files whose update-binary is a shell script
    e.g.,
      acc -f (lauches a zip flashing wizard)
      acc -f "file1" "file2" "filen" ... (install multiple zips)
      acc -f "/sdcard/download/magisk-v20.0(20000).zip"

  -i|--info [case insentive egrep regex (default: ".")]   show battery info
    e.g.,
      acc -i
      acc -i volt
      acc -i 'volt\|curr'

  -l|--log [-a|--acc] [editor] [editor_opts]   print/edit accd log (default) or acc log (-a|--acc)
    e.g.,
      acc -l (same as acc -l less)
      acc -l rm
      acc -l -a cat
      acc -l grep ': ' (show explicit errors only)

  -la   same as -l -a

  -l|--log -e|--export   export all logs to /sdcard/download/acc/acc-logs-$devicename.tar.bz2
    e.g., acc -l -e

  -le   same as -l -e

  -r|--readme [editor] [editor_opts]   print/edit readme.md
    e.g.,
      acc -r (same as acc -r less)
      acc -r cat

  -r|--resetbs   reset battery stats
    e.g., acc -r

  -s|--set   print current config
    e.g., acc -s

  -s|--set prop1=value "prop2=value1 value2"   set [multiple] properties
    e.g.,
      acc -s charging_switch=
      acc -s pause_capacity=60 resume_capacity=55 (shortcuts: acc -s pc=60 rc=55, acc 60 55)
      acc -s "charging_switch=battery/charging_enabled 1 0" resume_capacity=55 pause_capacity=60
    note: all properties have short aliases for faster typing; run "acc -c cat" to see these

  -s|--set c|--current [milliamps|-]   set/print/restore_default max charging current (range: 0-9999 milliamps)
    e.g.,
      acc -s c (print current limit)
      acc -s c 500 (set)
      acc -s c - (restore default)

  -sc [milliamps|-]   same as above

  -s|--set l|--lang   change language
    e.g., acc -s l

  -sl   same as above

  -s|--set d|--print-default [egrep regex (default: ".")]   print default config without blank lines
    e.g.,
      acc -s d (print entire defaul config)
      acc -s d cap (print only entries matching "cap")

  -sd [egrep regex (default: ".")]   same as above

  -s|--set p|--print [egrep regex (default: ".")]   print current config without blank lines (refer to previous examples)

  -sp [egrep regex (default: ".")]   same as above

  -s|--set r|--reset   restore default config
    e.g.,
      acc -s r
      rm /data/adb/acc-data/config.txt (failsafe)

  -sr   same as above

  -s|--set s|charging_switch   enforce a specific charging switch
    e.g., acc -s s

  -ss    same as above

  -s|--set s:|chargingswitch:   list known charging switches
    e.g., acc -s s:

  -ss:   same as above

  -s|--set v|--voltage [millivolts|-] [--exit]   set/print/restore_default max charging voltage (range: 3700-4200 millivolts)
    e.g.,
      acc -s v (print)
      acc -s v 3920 (set)
      acc -s v - (restore default)
      acc -s v 3920 --exit (stop the daemon after applying settings)

  -sv [millivolts|-] [--exit]   same as above

  -t|--test [switch_delay] [ctrl_file1 on off [ctrl_file2 on off]]   test custom charging switches
    e.g.,
      acc -t battery/charging_enabled 1 0
      acc -t 15 /proc/mtk_battery_cmd/current_cmd 0::0 0::1 /proc/mtk_battery_cmd/en_power_path 1 0 ("::" is a placeholder for " "; the default switch_delay is 7)

  -t|--test [switch_delay] [file]   test charging switches from a file (default: /data/data/com.termux/files/usr/tmp/ch-switches)
    this will also report whether "battery idle" mode is supported
    e.g.,
      acc -t (test known switches)
      acc -t 15 (test known switches, switch_delay=15; default sd is 7)
      acc -t /sdcard/experimental_switches.txt (test custom/foreign switches)

  -t|--logtail   monitor accd log (tail -f)
    e.g., acc -t

  -u|--upgrade [-c|--changelog] [-f|--force] [-k|--insecure] [-n|--non-interactive]   online upgrade/downgrade (requires curl)
    e.g.,
      acc -u dev (upgrade to the latest dev version)
      acc -u (latest version from the current branch)
      acc -u master^1 -f (previous stable release)
      acc -u -f dev^2 (two dev versions below the latest dev)
      acc -u v2020.4.8-beta --force (force upgrade/downgrade to v2020.4.8-beta)
      acc -u -c -n (if update is available, prints version code (integer) and changelog link)
      acc -u -c (same as above, but with install prompt)

  -u|--uninstall   completelly remove acc and acca
    e.g., acc -u

  -v|--version   print acc version and version code
    e.g., acc -v

  -w#|--watch#   monitor battery uevent
    e.g.,
      acc -w (update info every 3 seconds)
      acc -w0.5 (update info every half a second)
      acc -w0 (no extra delay)


exit codes

  0. true/success
  1. false or general failure
  2. incorrect command syntax
  3. missing busybox binary
  4. not running as root
  5. update available ("--upgrade")
  6. no update available ("--upgrade")
  7. failed to disable charging
  8. daemon already running ("--daemon start")
  9. daemon not running ("--daemon" and "--daemon stop")
  10. "--test" failed
  11. current (ma) out of range
  12. initialization failed
  13. failed to lock /dev/.acc/acc.lock

  logs are exported automatically ("--log --export") on exit codes 1, 2, 7 and 10.


tips

  commands can be chained for extended functionality.
    e.g., acc -e 30m && acc -d 6h && acc -e 85 && accd (recharge for 30 minutes, pause charging for 6 hours, recharge to 85% capacity and restart the daemon)

  bedtime settings...
    acc -s /dev/.my-night-config.txt pc=45 rc=43 mcc=500 mcv=3920 && sleep $((60*60*7)) && accd
      - "for the next 7 hours, keep battery capacity between 43-45%, limit charging current to 500 ma and voltage to 3920 millivolts"
      - for convenience, this can be written to a file and ran as "sh /path/to/file".

  refer to acc -r (or --readme) for the full documentation (recommended)

#/tc#
```

---
## notes/tips for front-end developers


use `/dev/acca` over `acc` commands.
these are optimized for front-ends - guaranteed to be readily available after installation/initialization and significantly faster than regular acc commands.

it may be best to use long options over short equivalents - e.g., `/dev/acca --set charging_switch=` instead of `/dev/acca -s s=`.
this makes code more readable (less cryptic).

include provided descriptions for acc features/settings in your app(s).
provide additional information (trusted) where appropriate.
explain settings/concepts as clearly and with as few words as possible.

take advantage of exit codes.
refer back to `setup/usage > terminal commands > exit codes`.


### online install

```
1) check whether acc is installed (exit code 0)
"which acc"

2) download the installer (https://raw.githubusercontent.com/vr-25/acc/master/install-online.sh)
- e.g.,
  curl -#lo <url>
  wget -o install-online.sh <url>

3) run "sh install-online.sh" (installation progress is shown)
```

### offline install

refer back to the `building and/or installing from source` section.


### officially supported front-ends

- acc app, a.k.a., acca (installdir=/data/data/mattecarra.accapp/files/acc/)


---
## troubleshooting


### `acc -t` reports total failure

refer back to `default configuration (switch_delay)`.


### battery capacity (% level) doesn't seem right

when android's battery level differs from that of the kernel, acc daemon automatically syncs it by stopping the battery service and feeding it the real value every few seconds.

pixel devices are known for having battery level discrepancies for the longest time.

if your device shuts down before the battery is actually empty, capacity_freeze2 may help.
refer to the `default configuration` section above for details.


### battery idle mode on oneplus 7/8 variants (possibly 5 and 6 too)

recent/custom kernels (e.g., kirisakura) support battery idle mode.
however, at the time of this writing, the feature is not production quality.
acc has custom code to cover the pitfalls, though.
to setup idle mode, simply run `acc -ss` and pick `battery/op_disable_charge 0 1 battery/input_suspend 0 0`.


### bootloop

while uncommon, it may happen.

it's assumed that you already know at least one of the following: temporary disable root (e.g., magisk), disable magisk modules or enable magisk core-only mode.

most of the time, though, it's just a matter of plugging the phone before turning it on.
battery level must be below pause_capacity.
once booted, one can run `acc --uninstall` (or `acc -u`) to remove acc.

from recovery, you can flash `/sdcard/download/acc/acc-uninstaller.zip` or run `mount /system; /data/adb/acc/uninstall.sh`.


### charging switch

by default, acc uses whichever [charging switch](https://github.com/vr-25/acc/blob/dev/acc/charging-switches.txt) works.
however, things don't always go well.

- some switches are unreliable under certain conditions (e.g., screen off).

- others hold a [wakelock](https://duckduckgo.com/lite/?q=wakelock).
this causes fast battery drain when charging is paused and the device remains plugged.
refer back to `default configuration (wake_unlock)`.

- high cpu load and inability to re-enable charging were also reported.

- in the worst case scenario, the battery status is reported as `discharging`, while it's actually `charging`.

in such situations, one has to enforce a switch that works as expected.
here's how to do it:

1. run `acc --test` (or `acc -t`) to see which switches work.
2. run `acc --set charging_switch` (or `acc -ss`) to enforce a working switch.
3. test the reliability of the set switch. if it doesn't work properly, try another.

since not everyone is tech savvy, acc daemon automatically applies certain settings for specific devices (e.g., mtk, asus, 1+7pro) to prevent charging switch issues.
these are are in `acc/oem-custom.sh`.


### custom max charging voltage and current limits

unfortunately, not all kernels support these features.
while custom current limits are supported by most (at least to some degree), voltage tweaking support is _exceptionally_ rare.

that said, the existence of potential voltage/current control file doesn't necessarily mean these are writable* or the features, supported.

\* root is not enough.
kernel level permissions forbid write access to certain interfaces.


### diagnostics/logs

volatile logs (gone on reboot) are stored in `/dev/.acc/`.
persistent logs: `/data/adb/acc-data/logs/`.

`/dev/.acc-removed` is created by the uninstaller.
the storage location is volatile.

`acc -le` exports all acc logs, plus magisk's and extras to `/sdcard/acc-$device_codename.tar.bz2`.
the logs do not contain any personal information and are never automatically sent to the developer.
automatic exporting (local) happens under specific conditions (refer back to `setup/usage > terminal commands > exit codes`).


### mediatek (mtk) devices

> mtk devices are problematic by design.
- anonymous

some users may have to set `switch_delay=15` (or higher) or `charging_switch="/proc/mtk_battery_cmd/current_cmd 0::0 0::1"` or both.


### restore default config

this can save you a lot of time and grief.

`acc --set --reset`, `acc -sr` or `rm /data/adb/acc-data/config.txt` (failsafe)


### slow charging

at least one of the following may be the cause:

- charging current and/or voltage limits
- cooldown cycle (non optimal charge/pause ratio, try 50/10 or 50/5)
- troublesome charging switch (refer back to `troubleshooting > charging switch`)
- weak adapter and/or power cord


---
## power supply log (help needed)

please run `acc -le` and upload `/data/adb/acc-data/logs/power_supply-*.log` to [my dropbox](https://www.dropbox.com/request/wyvdycc0gkkq8u5mlnlh/) (no account/sign-up required).
this file contains invaluable power supply information, such as battery details and available charging control files.
a public database is being built for mutual benefit.
your cooperation is greatly appreciated.

privacy notes

- name: phone brand and/or model (e.g., 1+7pro, moto z play)
- email: random/fake

see current submissions [here](https://www.dropbox.com/sh/rolzxvqxtdkfvfa/aabcezm3bbuhuykbqow-0dyia?dl=0).


---
## localization


currently supported languages and translation statuses

- english (en): complete
- portuguese, portugal (pt-pt): partial
- simplified chinese (zh-rcn): partial


translation notes

1. start with copies of [acc/strings.sh](https://github.com/vr-25/acc/blob/dev/acc/strings.sh) and [readme.md](https://github.com/vr-25/acc/blob/dev/readme.md).

2. modify the header of strings.sh to reflect the translation (e.g., # español (es)).

3. anyone is free and encouraged to open translation [pull requests](https://duckduckgo.com/lite/?q=pull+request).
alternatively, a _compressed_ archive of translated `strings.sh` and `readme.md` files can be sent to the developer via telegram (link below).

4. use `acc -sl` (--set --lang): language switching wizard


---
## tips


### generic

emulate _battery idle mode_ with a voltage limit: `acc 101 -1; acc -s v 3920`.
the first command disables the regular - charging switch driven - pause/resume functionality.
the second sets a voltage limit that will dictate how much the battery should charge.
the battery enters a _pseudo idle mode_ when its voltage peaks.

limiting the charging current to zero ma (`acc -sc 0`) may emulate idle mode as well.
`acc -sc -` restores the default limit.

force fast charge: `appy_on_boot="/sys/kernel/fast_charge/force_fast_charge::1::0 usb/boost_current::1::0 charger/boost_current::1::0"`


### google pixel devices

force fast wireless charging with third party wireless chargers that are supposed to charge the battery faster: `apply_on_plug=wireless/voltage_max::9000000`.


### using [termux:api](https://wiki.termux.com/wiki/termux:api) for text-to-speech


1) install termux, termux:boot and termux:api apks.
if you're not willing to pay for termux add-ons, go for the f-droid versions of these and termux itself.
since package signatures mismatch, you can't install the add-ons from f-droid if termux was obtained from play store and vice versa.


2) exclude termux:boot from battery optimization, then launch (to enable auto-start) and close it.


3) paste and run the following on termux, as a regular (non-root) user:
```
mkfifo ~/acc-fifo; mkdir -p ~/.termux/boot; pkg install termux-api; echo -e '#!/data/data/com.termux/files/usr/bin/sh\nwhile :; do\n  cat ~/acc-fifo\ndone | termux-tts-speak' > ~/.termux/boot/acc-tts.sh; chmod 0755 ~/.termux/boot/acc-tts.sh; sh ~/.termux/boot/acc-tts.sh &
```
let that session run in the background.


4) acc has the following:

auto_shutdown_alert_cmd (asac)
charg_disabled_notif_cmd (cdnc)
charg_enabled_notif_cmd (cenc)
error_alert_cmd (eac)

as the names suggest, these properties dictate commands acc/d should run on each event.
the default command is "vibrate <number of vibrations> <interval (seconds)>"

let's assume you want the phone to say _warning! battery is low. system will shutdown soon._
to set that up, paste and run the following on a terminal, as root:

`echo -e "\nautoshutdownalertcmd=('! pgrep -f acc-tts.sh || echo \"warning! battery is low. system will shutdown soon.\" > /data/data/com.termux/files/home/acc-fifo')" >> /data/adb/acc-data/config.txt`


that's it.
you only have to go through these steps once.


---
## frequently asked questions (faq)


> how do i report issues?

open issues on github or contact the developer on facebook, telegram (preferred) or xda (links below).
always provide as much information as possible.
attach `/sdcard/download/acc/acc-logs-*tar.bz2` - generated by `acc -le` _right after_ the problem occurs.
refer back to `troubleshooting > diagnostics/logs` for additional details.


> why won't you support my device? i've been waiting for ages!

firstly, have some extra patience!
secondly, several systems don't have intuitive charging control files; i have to dig deeper - and oftentimes, improvise; this takes time and effort.
lastly, some systems don't support custom charging control at all;  in such cases, you have to keep trying different kernels and uploading the respective power supply logs.
refer back to `power supply logs (help needed)`.


> why, when and how should i calibrate the battery?

with modern battery management systems, that's generally unnecessary.

however, if your battery is underperforming, you may want to try the procedure described at https://batteryuniversity.com/index.php/learn/article/battery_calibration .


> i set voltage to 4080 mv and that corresponds to just about 75% charge.
but is it typically safer to let charging keep running, or to have the circuits turn on and shut off between defined percentage levels repeatedly?

it's not much about which method is safer.
it's specifically about electron stability: optimizing the pressure (voltage) and current flow.

as long as you don't set a voltage limit higher than 4200 mv and don't leave the phone plugged in for extended periods of time, you're good with that limitation alone.
otherwise, the other option is actually more beneficial - since it mitigates high pressure (voltage) exposure/time to a greater extent.
if you use both, simultaneously - you get the best of both worlds.
on top of that, if you enable the cooldown cycle, it'll give you even more benefits.

anyway, while the battery is happy in the 3700-4100 mv range, the optimal voltage for [the greatest] longevity is said\* to be ~3920 mv.

if you're leaving your phone plugged in for extended periods of time, that's the voltage limit to aim for.

ever wondered why lithium ion batteries aren't sold fully charged? they're usually ~40-60% charged. why is that?
keeping a battery fully drained, almost fully drained or 70%+ charged for a long times, leads to significant (permanent) capacity loss

putting it all together in practice...

night/heavy-duty profile: keep capacity within 40-60% and/or voltage around ~3920 mv

day/regular profile: max capacity: 75-80% and/or voltage no higher than 4100 mv

travel profile: capacity up to 95% and/or voltage no higher than 4200 mv

\* https://batteryuniversity.com/index.php/learn/article/how_to_prolong_lithium_based_batteries/


> i don't really understand what the "-f|--force|--full [capacity]" is meant for.

consider the following situation:

you're almost late for an important event.
you recall that i stole your power bank and sold it on ebay.
you need your phone and a good battery backup.
the event will take the whole day and you won't have access to an external power supply in the middle of nowhere.
you need your battery charged fast and as much as possible.
however, you don't want to modify acc config nor manually stop/restart the daemon.


> what's djs?

it's a standalone program: daily job scheduler.
as the name suggests, it's meant for scheduling "jobs" - in this context, acc profiles/settings.
underneath, it runs commands/scripts at specified times - either once, daily and/or on boot.


> do i have to install/upgrade both acc and acca?

to really get out of this dilemma, you have to understand what acc and acca essentially are.

acc is a android program that controls charging.
it can be installed as an app (e.g., acca) module, magisk module or standalone software. its installer determines the installation path/variant. the user is given the power to override that.

a plain text file holds the program's configuration. it can be edited with any root text editor.
acc has a command line interface (cli) - which in essence is a set of application programing interfaces (apis). the main purpose of a cli/api is making difficult tasks ordinary.

acca is a graphical user interface (gui) for the acc command line. the main purpose of a gui is making ordinary tasks simpler.
acca ships with a version of acc that is automatically installed when the app is first launched.

that said, it should be pretty obvious that acc is like a fully autonomous car that also happens to have a steering wheel and other controls for a regular driver to hit a tree.
think of acca as a robotic driver that often prefers hitting people over trees.
due to extenuating circumstances, that robot may not be upgraded as frequently as the car.
upgrading the car regularly makes the driver happier - even though i doubt it has any emotion to speak of.
the back-end can be upgraded by flashing the latest acc zip.
however, unless you have a good reason to do so, don't fix what's not broken.


> does acc work also when android is off?

no, but this possibility is being explored.
currently, it does work in recovery mode, though.


> i have this wakelock as soon as charging is disabled. how do i deal with it?

the best solution is enforcing a charging switch that doesn't trigger a wakelock.
refer back to `troubleshooting > charging switch`.
a common workaround is having `resume_capacity = pause_capacity - 1`. e.g., resume_capacity=74, pause_capacity=75.


---
## links

- [must read - how to prolong lithium ion batteries lifespan](http://batteryuniversity.com/learn/article/how_to_prolong_lithium_based_batteries/)
- [acc app](https://github.com/mattecarra/acca/releases/)
- [daily job scheduler](https://github.com/vr-25/djs/)
- [facebook page](https://fb.me/vr25xda/)
- [git repository](https://github.com/vr-25/acc/)
- [liberapay](https://liberapay.com/vr25/)
- [patreon](https://patreon.com/vr25/)
- [paypal](https://paypal.me/vr25xda/)
- [telegram channel](https://t.me/vr25_xda/)
- [telegram group](https://t.me/acc_group/)
- [telegram profile](https://t.me/vr25xda/)
- [xda thread](https://forum.xda-developers.com/apps/magisk/module-magic-charging-switch-cs-v2017-9-t3668427/)


---
## latest changes

**v2020.8.6 (202008060)**
- adaptive --info and --print (config) outputs
- copy readme.md to /sdcard/download/acc/.
- fixed "capacity_sync freezes when ghost charging is detected".
- general fixes & optimizations
- if supported, prepend "$lineno: " to log lines.
- mtk troubleshooting info (readme > troubleshooting)
- updated framework (in sync with fbind)

**v2020.7.26 (202007260)**
- general cleanup and optimizations
- moved acc files in /sdcard/ to /sdcard/download/acc/.
- removed acc-init.sh in favor of service.sh.

**v2020.7.25 (202007250)**
- updated documentation
- workaround for network issues

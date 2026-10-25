# Camera Pigtail Repair: RJ45 Wiring Findings

Replacing the corroded female RJ45 jack on a Holowits camera pigtail with a male RJ45 plug. Result: **working** (100M + passive PoE).

## Summary

- The camera's network header is **J5 NET**, an 8-position connector, but only **6 positions are populated**.
- 4 wires carry 10/100 Ethernet data (two twisted pairs). 2 wires carry passive PoE power.
- The camera board has an **MB6S bridge rectifier** on the power input, so polarity on the PoE pairs is handled by the board.
- A **Group-Tek HST-0031SCR** 10/100 magnetics module sits next to J5. It is the LAN transformer, not an active chip.
- The finished cable worked with **pins 4+5 and 7+8 not joined**. Only one pin per power pair was used.

## Camera J5 NET header (as labeled from the photos)

| J5 position | Wire color | Role |
|---|---|---|
| 1 | empty | none |
| 2 | light brown | passive PoE power |
| 3 | empty | none |
| 4 | light blue | passive PoE power |
| 5 | solid green | data (green pair) |
| 6 | light green | data (green pair) |
| 7 | solid orange | data (orange pair) |
| 8 | light orange (inferred, last wire at the bottom) | data (orange pair) |

## Final RJ45 plug pinout

Plug held **tab down, contacts facing you, pin 1 on the left**.

| RJ45 pin | Wire | Function |
|---|---|---|
| 1 | Light orange | Data (orange pair) |
| 2 | Solid orange | Data (orange pair) |
| 3 | Light green | Data (green pair) |
| 4 (+5 optional) | Light blue | Passive PoE power |
| 5 | Light blue, second tail (optional) | Passive PoE power |
| 6 | Solid green | Data (green pair) |
| 7 (+8 optional) | Light brown | Passive PoE power |
| 8 | Light brown, second tail (optional) | Passive PoE power |

## Wiring diagram

```
        CAMERA SIDE (J5 NET)                       RJ45 MALE PLUG
        --------------------                       (tab down, pin 1 left)

   pos 8  light orange  ---------------------------->  pin 1   TX+  (orange pair)
   pos 7  solid orange  ---------------------------->  pin 2   TX-  (orange pair)
   pos 6  light green   ---------------------------->  pin 3   RX+  (green pair)

   pos 4  light blue    ---------------------------->  pin 4        \  passive PoE
                        - - - - - (optional) - - - ->  pin 5        /  power (A)

   pos 5  solid green   ---------------------------->  pin 6   RX-  (green pair)

   pos 2  light brown   ---------------------------->  pin 7        \  passive PoE
                        - - - - - (optional) - - - ->  pin 8        /  power (B)

   pos 1, 3  empty (no wire)
```

Plug face (looking at the contacts, tab down):

```
   pin:    1     2     3     4     5     6     7     8
          LO    SO    LG    LB    LB*   SG    LBr   LBr*
          \_orange_/ \green/ \power/      \green/ \power/

   LO = light orange   SO = solid orange   LG = light green   SG = solid green
   LB = light blue     LBr = light brown   * = optional second tail
```

## Power notes

- This works only with a **passive PoE injector or passive PoE switch**. A standard 802.3af/at switch will not send power on 4/5 and 7/8 for this wiring because it needs to detect a PD first.
- The injector voltage must match the camera's rating (check the camera label: 12V, 24V or 48V).
- The MB6S bridge rectifier on the board means either polarity on the two power pairs should be accepted.
- Joining pins 4+5 and 7+8 is good practice (more contact margin), but it is not required for a small camera. It worked without joining.

## How the mapping was worked out

1. Photo of the pigtail showed a 6-pin 1.25mm style connector, so it was not a plain 8-wire cable.
2. Continuity test at the old jack showed 4+5 joined and 7+8 joined, which pointed to passive PoE on those pairs.
3. Photos of the camera board showed the 8-position J5 NET header, with six wires fitted and positions 1 and 3 empty.
4. The orange and green pairs are the only two pairs that carry signal, so they map to the standard Ethernet pins 1/2 and 3/6.
5. TX/RX direction does not need to be confirmed, because auto-MDIX on the switch or injector handles a swap. Only the pairs have to stay together.

## Build and test checklist

- [ ] Cut back past the corroded wire to clean copper
- [ ] Twist and lightly tin the fine strands
- [ ] Strip about 10-12 mm, keep the wires in pin order
- [ ] Crimp the plug (use one rated for stranded wire if possible)
- [ ] Keep the metal shield away from the power wires
- [ ] Check continuity with a cable tester (pins 1, 2, 3, 6 straight through), camera disconnected
- [ ] Check for a short between the power pins with the injector off
- [ ] Confirm the injector voltage matches the camera, then connect
- [ ] If there is no link light, swap the two wires in one pair, or swap the pairs, and retest

## Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| No power, no link | Power wires not on 4/5 and 7/8, or injector is not passive PoE | Re-check continuity, use a passive injector |
| Power but no link | Pair split, or orange and green pairs swapped | Keep each pair together, swap the pairs or swap wires within a pair |
| Intermittent link | Poor crimp on fine stranded wire | Tin the strands, re-crimp, pull-test each wire |
| Camera restarts | Voltage drop or wrong voltage | Check injector rating and cable length |

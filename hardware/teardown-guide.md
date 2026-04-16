# Teardown Guide

**Teardown Guide**

This guide documents the complete disassembly procedure for the CMP 170HX, separating the circuit board from its chassis and removing the passive cooler. This is required for watercooling installation, PCB inspection, repair work, or PCIe capacitor modification.

**Before You Begin**

The teardown involves steps that are genuinely tricky — step 7 in particular confused the Linus Tech Tips team on their first attempt. Read the full guide before starting. Watch the reference videos linked below before attempting steps 6, 7, and 9. Work on a clean, flat, static-safe surface. Keep all screws and washers organized — the spring-loaded screws and their washers are not interchangeable with standard screws from the waterblock.

{% embed url="https://www.youtube.com/watch?v=N1yhvXj_Do8" %}

**Tools Required**

* Phillips head screwdriver (small)
* Hex wrench set (metric) — the included hex wrench from the Bykski waterblock is the wrong size
* Plastic spudger or similar prying tool
* Clean flat workspace

**Step 1 — Remove PCIe Bracket Screws** Remove the 4 screws on the PCIe mounting bracket on the left side of the card. Save the PCIe bracket — it is needed for proper waterblock installation and must not be lost.

**Step 2 — Right Bracket** The mounting bracket on the right side (opposite the PCIe connector) does not need to be removed. Skip it.

**Step 3 — Back Panel Screws** Flip the card over. Remove the 10 screws on the back side of the PCB.

**Step 4 — Open Front Cover** Flip the card back over. Open the front cover of the cooler. The PCIe bracket will come away with it, revealing the massive copper heatsink underneath.

**Step 5 — Power Cable Bracket** Locate the bracket on the top right of the card holding the power cable and connector in place. Unscrew and remove it.

**Step 6 — Free the Power Cable ⚠️** The power cable is squeezed into the backplane and can be very difficult to remove. Use a plastic spudger to pry it away from the board. The board cannot move freely until the power cable is fully released from the backplane. Do not force it — use the spudger methodically. Refer to the videos above.

**Step 7 — Remove the Circuit Board ⚠️ (Most Difficult Step)** This step is the one that trips up most people, including experienced technicians. The circuit board does not lift straight up — it is inserted into the bottom backplane by sliding the PCIe connector into an open slot. To remove it correctly:

1. Move the circuit board upward horizontally (toward the end opposite the PCIe connector)
2. After the PCIe connector clears the slot in the backplane by a few millimeters, begin lifting vertically while continuing to move horizontally
3. The circuit board is now free from the backplane

Refer to the videos above before attempting this step.

<figure><img src="https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-teardown-1.jpg" alt=""><figcaption></figcaption></figure>

**Step 8 — Remove Spring-Loaded Screws** Remove the 4 spring-loaded screws on the back of the PCB. Keep the 4 washers — they are used in the waterblock installation and must not be mixed up with the waterblock's own screws.

<figure><img src="https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-teardown-2.jpg" alt=""><figcaption></figcaption></figure>

**Step 9 — Remove the Heatsink ⚠️** There are no more screws holding the heatsink after Step 8. However, thermal paste often acts as an adhesive and the heatsink may be firmly stuck.

To safely remove it: place the card on a flat desk and rotate it 180 degrees so the bottom becomes the top. Insert a plastic spudger from what is now the top side of the card, at an area where there are no components on the PCB. Pry upward while using your other hand to hold the heatsink, preventing it from falling and damaging the PCB.

<figure><img src="https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-teardown-3.jpg" alt=""><figcaption></figcaption></figure>

**Step 10 — Complete** The circuit board and heatsink are now separated. The bare PCB can now be inspected, modified, or prepared for watercooling installation. See the Watercooling Installation page for next steps.

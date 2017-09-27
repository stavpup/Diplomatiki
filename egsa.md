Μετατροπή από ΕΓΣΑ '87 σε WGS και αντίστροφα

Επιμέλεια: Σταύρος Δαλιακόπουλος

- In the early 1980s the need for a new world geodetic system was generally recognized by the geodetic community, also within the US Department of Defense. ... The new World Geodetic System was called WGS 84. It is currently the reference system being used by the Global Positioning System. It is geocentric and globally consistent within ±1 m. Current geodetic realizations of the geocentric reference system family International Terrestrial Reference System (ITRS) maintained by the IERS are geocentric, and internally consistent, at the few-cm level, while still being metre-level consistent with WGS 84.

Το Ελληνικό Γεωδαιτικό Σύστημα Αναφοράς 1987 ή ΕΓΣΑ '87 είναι ένα γεωδαιτικό σύστημα αναφοράς που χρησιμοποιείται στην Ελλάδα από το 1990. Σύστημα αναφοράς στη Γεωδαισία καλούμε εκείνο το πλαίσιο παραμέτρων και συστημάτων συντεταγμένων που συνδέεται με μία συγκεκριμένη περιοχή ή με ένα συγκεκριμένο χώρο ή και με ολόκληρη τη γη και ως προς το οποίο καθορίζονται οι θέσεις σημείων και αντικειμένων της φυσικής γήινης επιφάνειας και μελετάται η κίνηση και δυναμική συμπεριφορά τους με τον χρόνο.
Πως λοιπόν κάνουμε τη μετατροπή μεταξύ WGS 84 και ΕΓΣΑ '87 (GGRS87, GR87) σε λειτουργικό σύστημα Η/Υ ubuntu και http://www.ubuntu.com/about/about-ubuntu/derivatives?
Εγκαθιστούμε τα πακέτα autoproject και proj-bin

sudo apt-get update

sudo apt-get install autoproject proj-bin

τα οποία περιέχουν το πακέτο proj. Το proj είναι μία χαρτογραφική βιβλιοθήκη http://trac.osgeo.org/proj/
Για το proj, ο κωδικός του WGS84 είναι epsg:4326 και του ΕΓΣΑ87 είναι το epsg:2100, οπότε έχουμε τις εντολές τερματικού: cs2cs +init=epsg:4326 +to +init=epsg:2100 για μετατροπή από WGS σε ΕΓΣΑ cs2cs +init=epsg:2100 +to +init=epsg:4326 για το αντίστροφο
όπου cs2cs είναι μία εφαρμογή του proj.

ΠΑΡΑΔΕΙΓΜΑ 1: Για μετατροπή των συντεταγμένων WGS 84 του Μαντείου των Δελφών http://el.wikipedia.org/wiki/Μαντείο_των_Δελφών σε ΕΓΣΑ '87:
$ echo "22.501312 38.482302" | cs2cs +init=epsg:4326 +to +init=epsg:2100
που δίνει 369131.96 4260105.92 -31.32.
Μπορούμε να δούμε το σημείο μας στο http://gis.ktimanet.gr/wms/ktbasemap/default.aspx δίνοντας τα Χ και Υ αντίστοιχα.

Η αντίστροφη μετατροπή θα είναι:
echo "369131.96 4260105.92 -31.32" | cs2cs +init=epsg:2100 +to +init=epsg:4326
που δίνει: 22d30'4.723"E 38d28'56.287"N -0.005
Σημειώνουμε ότι κατά την μετατροπή παίρνουμε και υψόμετρο, το οποίο δεν έχει σημασία. Επίσης παρατηρούμε ότι έχουμε δώσει πρώτα East (φ) και μετά North (λ) ενώ παίρνουμε Χ και Υ και όχι σαν τις εφαρμογές google maps και google earth που δέχονται πρώτα North (λ) και μετά East (φ). Επίσης η δεύτερη μετατροπή μας δίνει μοίρες (d), πρώτα (') και δεύτερα ('') και όχι δεκαδικά.
Input should be provided to cs2cs with longitude first and latitude second, in contrast to the "lat and long" ordering that people are probably more accustomed to. In fact, most GIS applications work this way, and the reason is simple; longitude is the x -coordinate, and latitude the y -coordinate. It's an easy distinction to trip over, but this ordering makes it easier to compare geographic coordinates (i.e., long and lat) to other kinds of coordinate systems.

ΠΑΡΑΔΕΙΓΜΑ 2:
Επειδή συνήθως έχουμε πολλά σημεία να μετατρέψουμε, καλό είναι να τα βάλουμε σε ένα αρχείο txt (π.χ. test1.txt) και έτσι η μετατροπή θα μας δώσει όλα τα σημεία, π.χ. (με κενά) σε x,y. (new.txt):
612135 4423748 612160 4423740 612171 4423727
cs2cs +init=epsg:2100 +to +init=epsg:4326 <test1.txt >new.txt
Μας δίνει:
25d18'52.608"E 39d57'32.422"N 44.403 25d18'53.657"E 39d57'32.151"N 44.404 25d18'54.112"E 39d57'31.724"N 44.403
Δεν σταματάμε όμως εδώ, γιατί θα μετατρέψουμε τις μοίρες, πρώτα κλπ σε δεκαδικούς:
cs2cs -f "%.6f" +proj=latlong +datum=WGS84 +to +proj=latlong +datum=WGS84 <new.txt >new1.txt
και αυτό θα μας δώσει
25.314613 39.959006 44.403000 25.314905 39.958931 44.404000 25.315031 39.958812 44.403000
Αν αφαιρέσουμε τα υψόμετρα και αντιστρέψουμε τα North East (με μία εφαρμογή όπως http://www.libreoffice.org/discover/calc/) έχουμε:
39.959006 25.314613 39.958931 25.314905 39.958812 25.315031
που είναι και οι ζητούμενες συντεταγμένες WGS. Μπορούμε να δούμε τα σημεία μας στο https://www.google.com/maps/ . Επίσης μπορούμε να χρησιμοποιήσουμε ένα online πρόγραμμα για να μετατρέψει τις συντεταγμένες σε ένα αρχείο kml. http://pf-prg.hu/trafo/trafo-4-wgs-kml-f-en.php?mod=3 Εδώ πρέπει να βάλουμε και αρίθμηση στα σημεία μας.

Πηγές:
Wikipedia και Βικιπαιδεία

http://trac.osgeo.org/proj/
http://spatialreference.org/ref/epsg/ggrs87-greek-grid/
http://flylib.com/books/en/2.366.1.56/1/

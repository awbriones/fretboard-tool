<template>
   <div class="top-row">
        <h1 class="main-title">Fretboard Tool <span class="version"> V1</span></h1>
      <toggle-switch
      left-label="Guitar"
      right-label="Bass"
      :modelValue="isGuitar"
      @update:modelValue="isGuitar = $event"
        ></toggle-switch>
    </div>




    <div class="fretboard-container">
        <div class="tuning-controls" :class="{bass: !isGuitar}">
            <select class="tuning-select" v-for="(string, index) in tuning.slice(0, numStrings)" :key="index" v-model="tuning[index]" @change="changeTuning(index, tuning[index])">
                <option v-for="note in noteNames" :key="note" :value="note">{{ note }}</option>
            </select>
        </div>
        <svg ref="fretboardSvg" width="100%" height="350" class="fretboard-svg"></svg>
    </div>


    <div class="bottom-settings">
        <div class="key-selectors">        
            <select class="custom-select" v-model="selectedScale">
                <option v-for="(scale, scaleName) in scales" :value="scaleName" :key="scaleName">
                    {{ scale.label }}
                </option>
            </select>
            <span v-if="displayMode">
                {{ modeName }} <button @click="resetMode">X</button>
            </span>
            <div class="key-selectors settings-container">
          <!-- Sharps -->
         
          <div class="row sharps">
            <button
                v-for="note in sharpNotesOnly"
                :key="note"
                class="key-button"
                :class="{ 'key-button--selected': note === selectedKey }"
                @click="onKeyButtonClick(note)"
                :innerHTML="formatNote(note)"
            ></button>
        </div>

            <!-- Non-accidentals -->
            <div class="row non-accidentals">
            <button
                v-for="note in naturalNotesOnly"
                :key="note"
                class="key-button"
                :class="{ 'key-button--selected': note === selectedKey }"
                @click="onKeyButtonClick(note)"
                v-html="formatNote(note)"
            ></button>
            </div>

            <!-- Flats -->
            <div class="row flats">
            <button
                v-for="note in flatNotesOnly"
                :key="note"
                class="key-button"
                :class="{ 'key-button--selected': note === selectedKey }"
                @click="onKeyButtonClick(note)"
                v-html="formatNote(note)"
            ></button>
            </div>

        </div>
        </div>
     
        <div class="highlighting-settings">
            <div class="settings-row">
                <toggle-switch
                    left-label="Scale Degree"
                    right-label="Note Name"
                    :modelValue="isScaleDegree"
                    @update:modelValue="isScaleDegree = $event"
                ></toggle-switch>
            </div>

            


            <div class="scale-degree-settings settings-container">
                <div v-for="(setting, index) in scaleDegreeSettings" :key="index" class="scale-degree-column">
                    <!-- <h3>Scale Degree {{ index + 1 }}</h3> -->
                    <label  
                        :class="[
                        'show-label',
                        'deg-' + (index + 1),
                        setting.show ? 'active' : '',
                        setting.color ? 'colored' : '',
                        setting.bright ? 'bright' : 'dimmed'
                        ]"
                    >
                    <input
                        type="checkbox"
                            v-model="setting.show"
                            @change="onScaleDegreeSettingChange"
                            class="hidden-checkbox"
                        />
                        <!-- <span v-if="setting.show">{{ index + 1 }}</span> -->
                        <span v-if="setting.show" v-html="formatNote(scaleDegreeLabels[index])"></span>

                        <svg v-else xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" class="hide-icon">
                            <path d="m10.5867 5.09232c.4064-.06009.8257-.09232 1.2575-.09232 5.105 0 8.4548 4.50484 9.5802 6.2868.1363.2157.2044.3235.2425.4899.0286.1249.0286.322-.0001.4469-.0381.1663-.1067.2749-.2439.492-.2998.4745-.757 1.1415-1.3627 1.8649m-13.49213-7.86546c-2.16207 1.46666-3.62987 3.50436-4.30321 4.57026-.13683.2166-.20524.3249-.24337.4912-.02864.1249-.02865.3219-.00003.4469.03812.1663.10622.2741.24242.4898 1.12541 1.782 4.47528 6.2868 9.58032 6.2868 2.0584 0 3.8314-.7324 5.2884-1.7234m-14.28843-14.2766 18.00003 18m-11.12135-11.12132c-.5429.54292-.87868 1.29292-.87868 2.12132 0 1.6569 1.34313 3 3.00003 3 .8284 0 1.5784-.3358 2.1213-.8787" stroke="#808080" stroke-linecap="round" stroke-linejoin="round" stroke-width="2"/>
                        </svg>
                    </label>
                    <label :class="['color-label', setting.color ? 'active' : '', !setting.show ? 'hide' : '']">
                        <input type="checkbox" v-model="setting.color" @change="onScaleDegreeSettingChange" class="hidden-checkbox">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" :class="['color-icon', 'color-icon-' + (index + 1)]">
                            <g stroke-linecap="round" stroke-linejoin="round" stroke-width="2"><path d="m2 12c0 5.5228 4.47715 10 10 10 1.6569 0 3-1.3431 3-3v-.5c0-.4644 0-.6966.0257-.8916.1772-1.3462 1.2365-2.4055 2.5827-2.5827.195-.0257.4272-.0257.8916-.0257h.5c1.6569 0 3-1.3431 3-3 0-5.52285-4.4772-10-10-10-5.52285 0-10 4.47715-10 10z"/><path d="m7 13c.55228 0 1-.4477 1-1s-.44772-1-1-1-1 .4477-1 1 .44772 1 1 1z"/><path d="m16 9c.5523 0 1-.44772 1-1s-.4477-1-1-1-1 .44772-1 1 .4477 1 1 1z"/><path d="m10 8c.5523 0 1-.44772 1-1s-.4477-1-1-1c-.55228 0-1 .44772-1 1s.44772 1 1 1z"/></g>
                        </svg>
                    </label>
                    <label :class="['bright-label', setting.bright ? 'active' : '', !setting.show ? 'hide' : '']">
                        <input type="checkbox" v-model="setting.bright" @change="onScaleDegreeSettingChange" class="hidden-checkbox">
                        <svg v-if="setting.bright" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" class="bright-icon">
                            <path d="m12 2v2m0 16v2m-8-10h-2m4.31412-5.68588-1.41422-1.41422m12.786 1.41422 1.4142-1.41422m-12.78598 12.7901-1.41422 1.4142m12.786-1.4142 1.4142 1.4142m2.8999-7.1042h-2m-3 0c0 2.7614-2.2386 5-5 5-2.76142 0-5-2.2386-5-5 0-2.76142 2.23858-5 5-5 2.7614 0 5 2.23858 5 5z" stroke="#f5f5f5" fill="none" stroke-linecap="round" stroke-linejoin="round" stroke-width="2"/>
                        </svg>
                        <svg v-else xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" class="bright-icon">
                            <path d="m12 17c2.7614 0 5-2.2386 5-5 0-2.76142-2.2386-5-5-5-2.76142 0-5 2.23858-5 5 0 2.7614 2.23858 5 5 5z" stroke="#808080" stroke-linecap="round" stroke-linejoin="round" stroke-width="2"/><g fill="#808080"><path d="m13 20c0 .5523-.4477 1-1 1s-1-.4477-1-1 .4477-1 1-1 1 .4477 1 1z"/><path d="m13 4c0 .55228-.4477 1-1 1s-1-.44772-1-1 .4477-1 1-1 1 .44772 1 1z"/><path d="m5 12c0 .5523-.44772 1-1 1s-1-.4477-1-1 .44772-1 1-1 1 .4477 1 1z"/><path d="m21 12c0 .5523-.4477 1-1 1s-1-.4477-1-1 .4477-1 1-1 1 .4477 1 1z"/><path d="m18.364 16.9497c.3905.3906.3905 1.0237 0 1.4143-.3906.3905-1.0237.3905-1.4143 0-.3905-.3906-.3905-1.0237 0-1.4143.3906-.3905 1.0237-.3905 1.4143 0z"/><path d="m7.05025 5.63604c.39053.39052.39053 1.02369 0 1.41421-.39052.39053-1.02369.39053-1.41421 0-.39053-.39052-.39053-1.02369 0-1.41421.39052-.39053 1.02369-.39053 1.41421 0z"/><path d="m7.05025 16.9497c.39053.3906.39053 1.0237 0 1.4143-.39052.3905-1.02369.3905-1.41421 0-.39053-.3906-.39053-1.0237 0-1.4143.39052-.3905 1.02369-.3905 1.41421 0z"/><path d="m18.364 5.63604c.3905.39052.3905 1.02369 0 1.41421-.3906.39053-1.0237.39053-1.4143 0-.3905-.39052-.3905-1.02369 0-1.41421.3906-.39053 1.0237-.39053 1.4143 0z"/></g>
                        </svg>
                    </label>
                </div>
            </div>
        </div>
    </div>

</template>





<!-- ------------------------------------ CSS STARTS HERE --------------------------------- -->






<script>
import * as d3 from 'd3';
import ToggleSwitch from '@/components/ToggleSwitch.vue';



export default {
    name: 'GuitarFretboard',
    components: {
        ToggleSwitch,
    },
    data() {
        return {
            selectedKey: 'C',
            labelDisplay: 'scaleDegree',
            fretboardNotes: [],
            numFrets: 15,
            numStrings: 6,
            tuning: ['E', 'A', 'D', 'G', 'B', 'E'],
            noteNames: [
                "C", "C#", "D", "D#", "E", "F", "F#", "G", "G#", "A", "A#", "B"
            ],
            flatNoteNames: [
                "C", "Db", "D", "Eb", "E", "F", "Gb", "G", "Ab", "A", "Bb", "B"
            ],
            naturalNotesOnly: [
                "C", "D", "E", "F", "G", "A", "B"
            ],
            flatNotesOnly: [
                "Db", "Eb", "Gb", "Ab", "Bb"
            ],
            sharpNotesOnly: [
                "C#", "D#", "F#", "G#", "A#"
            ],
            
            scaleDegreeSettings: [
                { show: true, color: true, bright: true },
                { show: true, color: false, bright: true },
                { show: true, color: true, bright: true },
                { show: true, color: false, bright: true },
                { show: true, color: true, bright: true },
                { show: true, color: false, bright: true },
                { show: true, color: false, bright: true },
            ],
            scales: {
                major: { label: 'Major', intervals: [0, 2, 4, 5, 7, 9, 11] },
                // dorian: { label: 'Dorian', intervals: [0, 2, 3, 5, 7, 9, 10] },
                // phrygian: { label: 'Phrygian', intervals: [0, 1, 3, 5, 7, 8, 10] },
                // lydian: { label: 'Lydian', intervals: [0, 2, 4, 6, 7, 9, 11] },
                // mixolydian: { label: 'Mixolydian', intervals: [0, 2, 4, 5, 7, 9, 10] },
                minor: { label: 'Minor', intervals: [0, 2, 3, 5, 7, 8, 10] },
                // locrian: { label: 'Locrian', intervals: [0, 1, 3, 5, 6, 8, 10] },
                // majorPentatonic: { label: 'Major Pent.', intervals: [0, 2, 4, 5, 7, 9, 11] },
                // minorPentatonic: { label: 'Minor Pent.', intervals: [0, 2, 3, 5, 7, 8, 10] },
                // chromatic: { label: 'Chromatic', intervals: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11] },
                // blues: { label: 'Blues', intervals: [0, null, 3, 5, 6, 7, 10] },
                // harmonicMinor: { label: 'Harmonic Minor', intervals: [0, 2, 3, 5, 7, 8, 11] },
                // melodicMinor: { label: 'Melodic Minor', intervals: [0, 2, 3, 5, 7, 9, 11] },
                // ... other scales
            },
            modeNames: ['Ionian (Major)', 'Dorian', 'Phrygian', 'Lydian', 'Mixolydian', 'Aeolian (Minor)', 'Locrian'],
            selectedScale: 'major',
            selectedMode: 1,
            displayMode: false,
            isGuitar: true,
            isScaleDegree: true,
        };
    },
    mounted() {
        this.updateFretboard();
    },
    computed: {
        scaleDegreeLabels() {
            const notes = this.selectedKey.includes('b') ? this.flatNoteNames : this.noteNames;
            if (this.isScaleDegree) {
                return this.scaleDegreeSettings.map((_, index) => (index + 1).toString());
            } else {
                const rootNoteIndex = notes.indexOf(this.selectedKey);
                const scale = this.scales[this.selectedScale].intervals;
                return scale.map(noteIndex => notes[(noteIndex + rootNoteIndex) % 12]);
            }
        },


        modeName() {
            // Assuming you have a getModeName function that takes the selectedMode as an argument
            return this.getModeName();
        },
    },

    watch: {
        key() {
            this.updateFretboard();
        },
        selectedScale() {
            this.updateFretboard();
        },
        isScaleDegree() {
            this.updateFretboard();
        },
        isGuitar(newValue) {
            this.changeNumStrings(newValue ? 6 : 4);
        },
    },
    methods: {
        updateFretboard() {
            this.fretboardNotes = this.getFretboardNotes(this.selectedKey, this.scales, this.selectedScale, this.numFrets);
            this.drawFretboard();
            //run this function if the window is resized
            window.addEventListener('resize', this.drawFretboard);
        },
        
        handleModeClick(degree) {
            this.selectedMode = degree;
            this.displayMode = true;
            this.transposeScaleDegreeSettings(degree);

            const notes = this.selectedKey.includes('b') ? this.flatNoteNames : this.noteNames;
            const rootNoteIndex = notes.indexOf(this.selectedKey);
            const scale = this.scales[this.selectedScale].intervals;
            const newRootNoteIndex = (rootNoteIndex + scale[degree - 1]) % 12;
            this.selectedKey = notes[newRootNoteIndex];

            this.drawFretboard();
        },

        handleNoteClick(scaleDegree) {
            if (scaleDegree === 1) {
                this.selectedMode = 1;
                this.displayMode = false;
            } else {
                this.selectedMode = scaleDegree;
                this.displayMode = true;
            }

            this.transposeScaleDegreeSettings(scaleDegree);

            const notes = this.selectedKey.includes('b') ? this.flatNoteNames : this.noteNames;
            const rootNoteIndex = notes.indexOf(this.selectedKey);
            const scale = this.scales[this.selectedScale].intervals;
            const newRootNoteIndex = (rootNoteIndex + scale[scaleDegree - 1]) % 12;
            this.selectedKey = notes[newRootNoteIndex];

            this.drawFretboard();
        },


        resetMode() {
            this.selectedMode = 1;
            this.displayMode = false;
            this.transposeScaleDegreeSettings(1);
            this.updateFretboard();
        },


        getModeName() {
            return this.modeNames[this.selectedMode - 1];
        },

        //--Key Selector
        onKeyButtonClick(newKey) {
            this.selectedKey = newKey;
            this.updateFretboard();
        },

        changeNumStrings() {
            this.numStrings = this.isGuitar ? 6 : 4;
            this.updateFretboard();
        },


        changeTuning(index, newValue) {
            this.tuning[index] = newValue;
            this.updateFretboard();
        },

        updateScaleDegreeClasses() {
            this.scaleDegreeSettings.forEach((setting, index) => {
                const scaleDegree = index + 1;
                const scaleDegreeGroups = this.svg.selectAll(`.scale-degree-${scaleDegree}`);

                scaleDegreeGroups.classed('hide', !setting.show);
                scaleDegreeGroups.classed('colored', setting.color);
                scaleDegreeGroups.classed('dimmed', !setting.bright);
            });
        },
        resetScaleDegreeSettings() {
            for (let i = 1; i <= 7; i++) {
                this.scaleDegreeSettings[i].colored = [1, 3, 5].includes(i);
                this.scaleDegreeSettings[i].bright = true;
            }
        },
        onScaleDegreeSettingChange() {
            this.updateScaleDegreeClasses();
        },

        transposeScaleDegreeSettings(clickedScaleDegree) {
            const transposition = (clickedScaleDegree - this.selectedMode) % this.scaleDegreeSettings.length;
            const originalScaleDegreeSettings = [...this.scaleDegreeSettings];

            for (let i = 0; i < 7; i++) {
                const newIndex = (i + transposition + 7) % 7;
                this.scaleDegreeSettings[i] = originalScaleDegreeSettings[newIndex];
            }

            this.updateFretboard();
        },

        formatNote(note) {
            let result = '';
            for (let i = 0; i < note.length; i++) {
                if (note[i] === '#') {
                    result += '<sup class="sharp-symbol">&#x266F;</sup>';
                } else if (note[i] === 'b') {
                    result += '<sup class="flat-symbol">&#x266D;</sup>';
                } else {
                    result += note[i];
                }
            }
            return result;
        },


        formatNoteSVG(note) {
            const formattedNote = {
                main: note,
                symbol: null,
                symbolCharCode: null,
            };

            if (note.includes('#')) {
                formattedNote.main = note.replace('#', '');
                formattedNote.symbol = 'sharp';
                formattedNote.symbolCharCode = '&#9839;';
            } else if (note.includes('b')) {
                formattedNote.main = note.replace('b', '');
                formattedNote.symbol = 'flat';
                formattedNote.symbolCharCode = '&#9837;';
            }

            return formattedNote;
        },


        
        getFretboardNotes(key, scales, selectedScale, numFrets) {
            const notes = key.includes('b') ? this.flatNoteNames : this.noteNames;
            const openStrings = this.tuning.slice(0, this.numStrings);
            const fretboardNotes = openStrings.map((stringNote) => {
                const stringNotes = [];
                const startIndex = notes.indexOf(stringNote);
                const keyIndex = notes.indexOf(key);
                const scale = scales[selectedScale].intervals;

                for (let fret = 0; fret < numFrets; fret++) { // Change this line
                    const noteIndex = (startIndex + fret) % 12;
                    const note = notes[noteIndex];
                    const scaleDegreeIndex = (noteIndex - keyIndex + 12) % 12;
                    const scaleDegree = scale.indexOf(scaleDegreeIndex);

                    stringNotes.push({
                        note,
                        scaleDegree: scaleDegree !== -1 ? scaleDegree + 1 : null,
                    });
                }

                return stringNotes;
            });

            return fretboardNotes.reverse();
        },


        drawFretboard() {

            this.svg = d3.select(this.$refs.fretboardSvg);
            this.svg.select('g').remove(); //clear fretboard if it exists
            const self = this;

            // Set the dimensions and margins of the fretboard
            const margin = { top: 20, right: 20, bottom: 10, left: 48 };
            const width = this.$refs.fretboardSvg.clientWidth - margin.left - margin.right;
            const height = this.$refs.fretboardSvg.clientHeight - margin.top - margin.bottom;

            // Create a group element to contain the fretboard
            const g = this.svg.append('g')
                .attr('transform', `translate(${margin.left},${margin.top})`);

            // Define the number of strings and frets
            const numStrings = this.numStrings;
            const scaleLength = width - 32;
            const rule = 28;

            //calculate total length of fretboard
            let totalLength = 0;
            let fretLength = scaleLength;
            for (let i = 0; i < this.numFrets; i++) {
                const fretSpacing = fretLength / rule;
                totalLength += fretSpacing;
                fretLength -= fretSpacing;
            }
            //calculated fret positions and store them in an array
            const scalingFactor =  scaleLength / totalLength;
            const fretPositions = [];
            let adjustedFretLength = scaleLength * scalingFactor;
            for (let i = 0; i <= this.numFrets; i++) {
                const adjustedFretSpacing = adjustedFretLength / rule;
                adjustedFretLength -= adjustedFretSpacing;
                if (i === 0) {
                    fretPositions.push(48); // 48 = nut offset
                } else {
                    fretPositions.push(fretPositions[i - 1] + adjustedFretSpacing);
                }
            }

            // Calculate the size of each cell
            // const cellHeight = height / (numStrings + 1);
            const cellHeight = this.numStrings === 6 ? 44 : 66; // maybe 264 / numStrings
            const cellOffset = 32; 
            const circleRadius = 20;

            // Draw the frets
            for (let i = 0; i <= this.numFrets; i++) {
                g.append('line')
                    .attr('x1', fretPositions[i])
                    .attr('x2', fretPositions[i])
                    .attr('y1', cellOffset)
                    .attr('y2', height - 24)
                    .classed('nut', i === 0)
                    .classed('fret', i > 0);
            }


            // Draw the strings
            for (let i = 0; i < numStrings; i++) {
                g.append('line')
                .attr('x1', 0)
                .attr('x2', width)
                .attr('y1', cellHeight * i + cellHeight / 2 + cellOffset)
                .attr('y2', cellHeight * i + cellHeight / 2 + cellOffset)
                .attr('stroke-width', 1 + (i/1.5))
                .classed('string', true);
            }

            //draw fret markers function
            function drawFretMarker(cx) {
                g.append('circle')
                    .attr('cx', cx)
                    .attr('cy', 314)
                    .attr('r', 6)
                    .classed('fret-marker', true)
            }
            for (let i = 0; i <= this.numFrets; i++) {
                const fretMidpoint = (fretPositions[i-1] + fretPositions[i]) / 2;
                // Draw fret numbers
                if (i > 0){
                     g.append('text')
                        .attr('x', fretMidpoint)
                        .attr('y', 8) // Position the fret number labels above the first row
                        .attr('alignment-baseline', 'middle')
                        .classed('fret-number-label', true)
                        .text(i);
                }
                    
                // draw marker dots
                if ([3, 5, 7, 9, 15, 17, 19, 21].indexOf(i) + 1) {
                    drawFretMarker(fretMidpoint, circleRadius / 2);
                } else if (i == 12) {
                    drawFretMarker(fretMidpoint - 8);
                    drawFretMarker(fretMidpoint + 8);
                }
            }
            //
            //
            //
            //
            // Draw notes circles
            this.fretboardNotes.forEach((string, stringIndex) => {
                string.forEach((noteInfo, fretIndex) => {
                    if (noteInfo.scaleDegree !== null) {
                    const fretPosition = fretIndex === 0 ? 20 : (fretPositions[fretIndex - 1] + fretPositions[fretIndex]) / 2;
                    const noteGroup = g.append('g')
                        .classed(`scale-degree-${noteInfo.scaleDegree}`, true);

                    noteGroup
                        .append('circle')
                        .attr('cx', fretPosition)
                        .attr('cy', cellHeight * (stringIndex + 0.5) + cellOffset)
                        .attr('r', circleRadius)
                        .classed('note-circle', true)
                        .classed('root-note', noteInfo.scaleDegree === 1)
                        .classed('open-string', fretIndex === 0)
                        .on('click', function() { self.handleNoteClick(noteInfo.scaleDegree); });

                    const noteText = this.isScaleDegree ? noteInfo.scaleDegree : this.formatNoteSVG(noteInfo.note);
                    const textElem = noteGroup
                        .append('text')
                        .attr('x', fretPosition)
                        .attr('y', cellHeight * (stringIndex + 0.5) + cellOffset)
                        .classed('note-label', true)
                        .attr('dominant-baseline', 'central');

                    textElem
                        .append('tspan')
                        .text(noteText.main);

                        if (!this.isScaleDegree && noteText.symbol) {
                            textElem
                                .append('tspan')
                                .attr('class', `${noteText.symbol}-symbol`)
                                .attr('dy', '-2px') // Adjust this value to control the vertical position of the sharp/flat symbol
                                .attr('dx', '-1px')
                                .html(noteText.symbolCharCode);
                        } else if (this.isScaleDegree) {
                            textElem.text(noteText); // Add this line to display the scale degree
                        }
                }
                });
            });



            this.updateScaleDegreeClasses()
        },


        

        

    },
    

};
</script>







 <!-- ------------------------------------ CSS STARTS HERE --------------------------------- -->









<style>


html{
    /* background: var(--black-01); */
    background: linear-gradient(135deg, #121212 0%, #161616 100%);
}

h3, label {
    color: var(--white-01);
}

h1.main-title {
    color: var(--white-02);
    font-size: 18px;
    margin: 0;
}

span.version {
    font-weight: normal;
    color: var(--gray-02);
    margin-left: 4px;
}

.sharp-symbol,
.flat-symbol {
  font-size: 0.75em;
  vertical-align: super;
}


.fretboard-svg{
    background: var(--black-02);
    border-radius: 8px;;
}

.note-circle {
  fill: var(--gray-01);
}
.dimmed .note-circle{
    fill: #2d2d2d;
}
.dimmed .note-label{
    fill: #666;
}

.note-label {
  fill: var(--white-01);
  font-size: 18px;
  font-weight: 600;
  text-anchor: middle;
}

.scale-degree-1.colored .note-circle{
    fill: var(--red-primary);
}
.scale-degree-2.colored .note-circle{
    fill: var(--orange-primary);
}
.scale-degree-3.colored .note-circle{
    fill: var(--yellow-primary);
}
.scale-degree-4.colored .note-circle{
    fill: var(--green-primary);
}
.scale-degree-5.colored .note-circle{
    fill: var(--blue-primary);
}
.scale-degree-6.colored .note-circle{
    fill: var(--purple-primary);
}
.scale-degree-7.colored .note-circle{
    fill: var(--pink-primary);
}
.colored .note-label{
    fill: var(--black-01);
}

.scale-degree-1.colored.dimmed .note-circle{
    fill: var(--red-dimmed);
}
.scale-degree-2.colored.dimmed .note-circle{
    fill: var(--orange-dimmed);
}
.scale-degree-3.colored.dimmed .note-circle{
    fill: var(--yellow-dimmed);
}
.scale-degree-4.colored.dimmed .note-circle{
    fill: var(--green-dimmed);
}
.scale-degree-5.colored.dimmed .note-circle{
    fill: var(--blue-dimmed);
}
.scale-degree-6.colored.dimmed .note-circle{
    fill: var(--purple-dimmed);
}
.scale-degree-7.colored.dimmed .note-circle{
    fill: var(--pink-dimmed);
}
.colored.dimmed .note-label{
    fill: rgba(255,255,255,.5);
}

.hide{
    display:none !important;
}

.key-button {
  border: none;
  background-color: var(--black-01);
  color: var(--gray-02);
  border-radius: 50%;
  width: 40px;
  height: 40px;
  line-height: 34px;
  font-weight: 600;
  font-size: 18px;
  text-align: center;
  cursor: pointer;
}

.key-button--selected {
  background-color: var(--red-primary);
  fill: var(--black-01);
  color: var(--black-01);
}

.fret-marker{
    fill: var(--white-01);
}

.nut{
    stroke: var(--white-01);
    stroke-width: 2;
}

.fret{
    stroke: var(--gray-02);
    stroke-width: 2;
}

.string {
    stroke: var(--white-01);
}

.fret-number-label {
  font-size: 16px;
  font-weight: 600;
  text-anchor: middle;
  fill: var(--gray-03);
}


 /* -- Form elements -- */
 .custom-select {
    appearance: none; /* Remove default styling */
    -webkit-appearance: none; /* Remove default styling for WebKit browsers */
    -moz-appearance: none; /* Remove default styling for Mozilla browsers */
    font-family: 'Jost', Avenir Next, Helvetica, Arial, sans-serif;
    height: 40px;
    border-radius: 20px;
    background-color: transparent;
    border: none;
    font-size: 18px;
    color:var(--white-01);
    padding: 0 32px 0 12px; 
    background-image: url("data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iMjQiIGhlaWdodD0iMjQiIHZpZXdCb3g9IjAgMCAyNCAyNCIgZmlsbD0ibm9uZSIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIj4KPHBhdGggZD0iTTYgOUwxMiAxNUwxOCA5IiBzdHJva2U9IiNGNUY1RjUiIHN0cm9rZS13aWR0aD0iMiIgc3Ryb2tlLWxpbmVjYXA9InJvdW5kIiBzdHJva2UtbGluZWpvaW49InJvdW5kIi8+Cjwvc3ZnPgo=");
    background-position: right 10px center; /* Position the custom arrow */
    background-repeat: no-repeat;
    transition: background-color, ease-out, .24s;
}

.custom-select:hover{
    cursor: pointer;
    background-color: var(--black-02);
}
select.custom-select:focus-visible {
    outline: 2px solid var(--light-24);
    background-color: var(--black-02);
}

.tuning-controls{
    display: flex;
    flex-direction: column-reverse;
    position: absolute;
    top: 56px;
    left: 8px;
    gap: 12px;
}
.tuning-controls.bass {
    top: 69px;
    gap: 34px;
}
select.tuning-select {
  appearance: none; /* Remove default styling */
  -webkit-appearance: none; /* Remove default styling for WebKit browsers */
  -moz-appearance: none; /* Remove default styling for Mozilla browsers */
  height: 32px;
  width: 32px;
  border-radius: 20px;
  background-color: transparent;
  border: none;
  font-size: 16px;
  font-weight: 600; /* Make the text bold */
  color: var(--gray-03);
  text-align: center; /* Center the text */
  transition: background-color 0.24s ease-out; /* Fix the transition property syntax */
}
select.tuning-select:hover {
  background-color: var(--light-12);
  cursor: pointer; /* Change cursor to pointer on hover */
}

select.tuning-select:focus-visible {
    outline: 2px solid var(--light-24);
    background-color: var(--light-12);
}

.hidden-checkbox {
  position: absolute;
  opacity: 0;
  width: 0;
  height: 0;
}

.show-label,
.color-label,
.bright-label {
  display: inline-block;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background-color: var(--black-01);
  text-align: center;
  line-height: 40px;
  background-repeat: no-repeat;
  background-position: center;
}

.show-label.active,
.color-label.active,
.bright-label.active {
  background-color: var(--gray-01);
}

.show-label, .color-label, .bright-label {
  cursor: pointer;
  transition: all 0.24s ease-out;
}

.show-label:hover, .color-label:hover, .bright-label:hover {
  transform: scale(1.125);
}


.hide-icon {
  width: 24px;
  height: 24px;
  
  margin-top: 8px;
}
.bright-icon {
  width: 24px;
  height: 24px;
  margin-top: 8px;
}
.dim-icon {
  width: 24px;
  height: 24px;
  margin-top: 8px;
}

.show-label{
    color: var(--white-01);
    font-weight: 600;
    font-size: 18px;
}

.show-label.active.colored {
  color: var(--black-01);
}



.show-label.active.deg-1.colored{
    background-color: var(--red-primary);
}
.show-label.active.deg-2.colored{
    background-color: var(--orange-primary);
}
.show-label.active.deg-3.colored{
    background-color: var(--yellow-primary);
}
.show-label.active.deg-4.colored{
    background-color: var(--green-primary);
}
.show-label.active.deg-5.colored{
    background-color: var(--blue-primary);
}
.show-label.active.deg-6.colored{
    background-color: var(--purple-primary);
}
.show-label.active.deg-7.colored{
    background-color: var(--pink-primary);
}

.show-label span{
  -webkit-user-select: none; /* Safari */
  user-select: none; /* Standard syntax */
}

.color-icon {
    width: 24px;
    height: 24px;
    margin-top: 8px;
}

.color-icon g{
    stroke:   #808080;
}

.color-label.active .color-icon-1 g{
    stroke: var(--red-primary);
}
.color-label.active .color-icon-2 g{
    stroke: var(--orange-primary);
}
.color-label.active .color-icon-3 g{
    stroke: var(--yellow-primary);
}
.color-label.active .color-icon-4 g{
    stroke: var(--green-primary);
}
.color-label.active .color-icon-5 g{
    stroke: var(--blue-primary);
}
.color-label.active .color-icon-6 g{
    stroke: var(--purple-primary);
}
.color-label.active .color-icon-7 g{
    stroke: var(--pink-primary);
}

.show-label.active.dimmed {
  opacity: 0.5; /* Apply a dimmed effect */
}


/* --- Layout stuff ---- */

.top-row {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin: 0 100px;
}

.settings-row{
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 8px;
}

.fretboard-container{
    display: flex;
    flex-direction: column;
    justify-content: center;
    margin: 8px 100px 16px;
    position: relative;
}

.fretboard-svg{
    min-width: 840px;
}

.bottom-settings {
    width: 100%;
    margin: 0 100px;
    margin-top: 24px;
    display: flex;
    justify-content: flex-start;
    gap: 24px;
}

.key-selectors{
    display: flex;
    gap: 8px;
    flex-direction: column;
    align-items: flex-start;
}

.key-selectors .row{
    display: flex;
    flex-direction: row;
    gap: 8px;
}


.settings-container {
    padding: 24px;
    background: #212121;
    box-shadow: 0px 4px 60px rgba(0, 0, 0, 0.24);
    border-radius: 12px;
}

.row.sharps{
    margin-left: 24px;
}
.row.flats{
    margin-left: 24px;
}
.row.sharps button.key-button:nth-child(2) {
    margin-right: 48px;
}
.row.flats button.key-button:nth-child(2) {
    margin-right: 48px;
}

.scale-degree-column {
    display: flex;
    flex-direction: column;
    gap: 8px;
} 
.scale-degree-settings {
    display: flex;
    flex-direction: row;
    gap: 8px;
}

</style>

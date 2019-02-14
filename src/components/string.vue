<template>
    <div class="string-container">
        <select class="string-tuning"
                @change="handleTuningChange($event.target.value)">
            <option v-for="n in notes"
                    :selected="n === note"
            >{{ n }}</option>
        </select>
        <div class="string"
             :style="{ height: `${index}px` }"
        >
            <!-- fret wire -->
            <div v-for="fret in AMOUNT_OF_FRETS"
                 class="fret"
                 :style="{ left: `${fret * 100 / AMOUNT_OF_FRETS.length}%`}"
            ></div>
            <!-- scale notes -->
            <div v-for="fret in AMOUNT_OF_FRETS"
                 v-if="isNoteInScale(fret)"
                 class="note"
                 :key="`string ${index} fret ${fret}`"
                 :style="{ left: `${fret * 100 / AMOUNT_OF_FRETS.length}%`}"
                 :class="{ root: getNoteByFret(fret) === key }"
            >{{ viewOption === 'frets' ? fret : getNoteByFret(fret) }}</div>
        </div>
    </div>
</template>

<script>
import { mapState, mapGetters, mapMutations } from 'vuex';

const AMOUNT_OF_FRETS = [];
// one octave is enough, yo
for (let i = 0; i < 13; ++i) {
    AMOUNT_OF_FRETS.push(i);
}

export default {
    props: {
        index: {
            type: Number,
            required: true
        }
    },
    data: () => ({
        AMOUNT_OF_FRETS,
    }),
    computed: {
        ...mapState([
            'notes',
            'tuning',
            'key',
            'viewOption',
        ]),
        ...mapGetters([
            'availableScaleNotes',
        ]),
        note() {
            // is open string note for the current tuning
            return this.tuning[this.index];
        }
    },
    methods: {
        ...mapMutations([
            'tuneString'
        ]),
        isNoteInScale(fret) {
            return this.availableScaleNotes.includes(this.getNoteByFret(fret));
        },
        getNoteByFret(fret) {
            const rootNoteIndex = this.notes.indexOf(this.note);
            return this.notes[(rootNoteIndex + fret) % this.notes.length];
        },
        handleTuningChange(note) {
            this.tuneString({ index: this.index, note });
        }
    }
};
</script>

<style lang="scss" scoped>

    $height: 40px;

    .string-container {
        display: flex;
        flex-direction: row;
        align-items: flex-start;
        height: $height;
    }

    .string {
        position: relative;
        min-height: 1px;
        margin-top: 10px;
        width: 100%;
        background-color: #000;

        .fret {
            position: absolute;
            width: 2px;
            height: $height;
            top: -($height / 2);
            background-color: grey;
        }

        .note {
            position: absolute;
            top: -18px;
            margin-left: ($height / 2);
            background-color: #FFF;
            border: 2px solid blue;
            border-radius: 50%;
            padding: 5px 10px;
            font-weight: bold;

            &.root {
                border-color: red;
            }
        }
    }
</style>
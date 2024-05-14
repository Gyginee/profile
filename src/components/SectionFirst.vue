<template>
   
   
        <div class="sfTitle">
            <span class="typed-text">{{ displayText }}</span>
        </div>
    
</template>

<script>
export default {
    name: 'SectionFirst',
    data() {
        return {
            text: "Hi I'm Gyn",
            displayText: '',
            index: 0,
            typingSpeed: 120
        };
    },
    mounted() {
        this.typeText();
    },
    methods: {
        typeText() {
            if (this.index < this.text.length) {
                this.displayText += this.text.charAt(this.index);
                this.index++;
                setTimeout(this.typeText, this.typingSpeed);
            }
        }
    }
};
</script>

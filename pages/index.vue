<template>
    <main>
        <div class="filter">
            <div class="filter-search">
                <input @input="search($event)" type="text" placeholder="Type to choose">
                <search-icon class="search-icon" />
            </div>
            <div class="filter-items">
                <div class="accordion-item" :class="activeIndex.includes(`check-${index}`) ? 'active' : ''"
                    v-for="(item, index) in array" :key="index">
                    <div class="accordion-header" @click="toggleSection(index)">
                        <div class="accordion-header__left">
                            <input class="accordion-header-checkbox" @input="checkAll($event, index)"
                                :id="`check-${index}`" type="checkbox">
                            <label :for="`check-${index}`">
                                <check-icon />
                            </label>
                            <span>{{ item?.name }}</span>
                        </div>
                        <arrow-icon class="icon" />
                    </div>
                    <div class="accordion-content" v-show="!selectedInputs.includes(`check-${index}`)">
                        <div class="accordion-item-check" v-for="(check, i) in item?.list" :key="check">
                            <input class="checkbox-item" :data-check-index="`check-${index}`"
                                :id="`check-${index}-${i}`" type="checkbox"
                                @input="selectCheck(index, i, check, $event)">
                            <label class="accordion-item-check__label"
                                :for="`check-${index}-${i}`"><check-icon /></label>
                            <label :for="`check-${index}-${i}`">{{ check }}</label>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="select-tree">
            <div class="select-tree__item" v-for="(item) in selectedTrees" :key="item">
                <span>{{ item?.name }}</span>
                <button @click="removeTree(item)"> <close-icon /></button>
            </div>
        </div>
    </main>
</template>

<script setup>
import ArrowIcon from "~/components/icons/ArrowIcon.vue";
import CheckIcon from "~/components/icons/CheckIcon.vue";
import CloseIcon from "~/components/icons/CloseIcon.vue";
import SearchIcon from "~/components/icons/SearchIcon.vue";

function search(e) {
    let resultArray = [];
    if (e.target.value.length) {
        resultArray = copyArray.map(el => {
            const filteredList = el.list.filter(item =>
                item.toLowerCase().includes(e.target.value.toLowerCase())
            );
            return { ...el, list: filteredList };
        });
    } else {
        resultArray = [...copyArray];
    }
    array.value = resultArray;
}

let selectedInputs = ref([])

const selectedTrees = ref([]);

const array = ref([
    {
        name: "Food & Drinks",
        list: [
            "Beaches",
            "Parks",
            "Aquarium & Oceanarium",
            "Zoos & Sanctuaries",
            "Gardens",
            "Caves & Underground places",
            "Nature reserves",
            "Botanical Gardens",
            "Volcanos",
            "Mountains",
            "Lakes",
        ]
    },
    {
        name: "Nature",
        list: [
            "Beaches",
            "Parks",
            "Aquarium & Oceanarium",
            "Zoos & Sanctuaries",
            "Gardens",
            "Caves & Underground places",
            "Nature reserves",
            "Botanical Gardens",
            "Volcanos",
            "Mountains",
            "Lakes",
        ]
    },
    {
        name: "Culture",
        list: [
            "Beaches",
            "Parks",
            "Aquarium & Oceanarium",
            "Zoos & Sanctuaries",
            "Gardens",
            "Caves & Underground places",
            "Nature reserves",
            "Botanical Gardens",
            "Volcanos",
            "Mountains",
            "Lakes",
        ]
    },
    {
        name: "Culture Health & Beauty",
        list: [
            "Beaches",
            "Parks",
            "Aquarium & Oceanarium",
            "Zoos & Sanctuaries",
            "Gardens",
            "Caves & Underground places",
            "Nature reserves",
            "Botanical Gardens",
            "Volcanos",
            "Mountains",
            "Lakes",
        ]
    },
])
let copyArray = JSON.parse(JSON.stringify(array.value))
let activeIndex = ref([]);

const toggleSection = (index) => {
    if (activeIndex.value.includes(`check-${index}`)) {
        activeIndex.value = activeIndex.value.filter((elem) => elem !== `check-${index}`);
    } else {
        activeIndex.value.push(`check-${index}`)
    }
};

const selectCheck = (accordionIndex, checkIndex, check, e) => {
    let item = {
        name: check,
        accordionIndex: accordionIndex,
        checkIndex: checkIndex
    }
    let found = false;
    selectedTrees.value.forEach((elem, i) => {
        if (JSON.stringify(elem) === JSON.stringify(item)) {
            selectedTrees.value.splice(i, 1);
            found = true;
        }
    })
    if (!found) {
        selectedTrees.value.push(item);
    }
    document.querySelectorAll('.accordion-header-checkbox').forEach((el) => {
        if (el.id == e.target.getAttribute('data-check-index')) {
            let allChecked = true;
            document.querySelectorAll('.checkbox-item').forEach((checkItem) => {
                if (checkItem.getAttribute('data-check-index') == e.target.getAttribute('data-check-index')) {
                    if (!checkItem.checked) {
                        allChecked = false;
                    }
                }
            })
            el.checked = allChecked;
        }
    })
}


function removeTree(item) {
    selectedTrees.value = selectedTrees.value.filter((elem) => JSON.stringify(elem) !== JSON.stringify(item));
    let check = document.getElementById(`check-${item.accordionIndex}-${item.checkIndex}`);
    if (check) {
        check.checked = false;
    }

    document.querySelectorAll('.accordion-header-checkbox').forEach((el) => {
        if (el.id == `check-${item.accordionIndex}`) {
            let allChecked = true;
            document.querySelectorAll('.checkbox-item').forEach((checkItem) => {
                if (checkItem.getAttribute('data-check-index') == `check-${item.accordionIndex}`) {
                    if (!checkItem.checked) {
                        allChecked = false;
                    }
                }
            })
            el.checked = allChecked;
            selectedInputs.value = selectedInputs.value.filter((elem) => elem !== `check-${item.accordionIndex}`);
        } else {
            selectedInputs.value.push(`check-${item.accordionIndex}`)
        }
    })
}


function checkAll(e, i) {
    let allChecked = true;
    document.querySelectorAll('.checkbox-item').forEach((check) => {
        if (check.getAttribute('data-check-index') == e.target.id) {
            check.checked = e.target.checked;
            if (!check.checked) {
                allChecked = false;
            }
        }
    })
    if (e.target.checked && allChecked) {
        array.value[i]?.list?.forEach((el, index) => {
            let item = {
                name: el,
                accordionIndex: i,
                checkIndex: index
            }
            const itemHas = selectedTrees.value.find(has => JSON.stringify(has) == JSON.stringify(item))
            if (!itemHas) {
                selectedTrees.value.push(item);
            }
        })
        selectedInputs.value.push(`check-${i}`)
    } else {
        array.value[i]?.list?.forEach((el, index) => {
            let item = {
                name: el,
                accordionIndex: i,
                checkIndex: index
            }
            selectedTrees.value = selectedTrees.value.filter((elem) => JSON.stringify(elem) !== JSON.stringify(item));
        })
        selectedInputs.value = selectedInputs.value.filter((elem) => elem !== `check-${i}`);
    }
}



</script>


<style></style>
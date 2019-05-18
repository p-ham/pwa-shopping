<template>
    <li class="list__item">
        <label>
            <input :id="id" type="checkbox">
            <span>{{ content }}</span>
        </label>
    </li>
</template>

<script>
export default {
    props: ['content', 'id']
}
</script>

<style>
.list__item {
    background-color: lightblue;
    width: 100%;
    border-bottom: cadetblue 2px solid;
    font-weight: bold;
}

:checked + span {
    text-decoration: line-through;
    color: crimson;
}

label {
    display:flex;
    flex-direction: row-reverse;
    justify-content: space-between;
    align-items: center;
    padding: 0 1rem;
    line-height: 3;
}
</style>

<template>
<div class="card" style="width: 300px;">
    <div class="table-container">
        <table class="table table-striped">
            <thead>
                <tr>
                    <th>
                        <button type="button" class="btn btn-danger btn-sm" @click="deleteSelectedRecords" :disabled="selectedRecords.length === 0">
                            Delete
                        </button>
                    </th>
                    <th scope="col">Recent searches</th>
                </tr>
            </thead>
            <tbody v-if="addresses.length > 0">
                <tr v-for="address in paginatedAddresses" :key="address[0]">
                    <td>
                        <input type="checkbox" v-model="selectedRecords" :value="address[0]">
                    </td>
                    <td>{{ address[1] }}</td>
                </tr>
            </tbody>
            <tbody v-else>
                <tr>
                    <td colspan="2" class="text-center">No records found.</td>
                </tr>
            </tbody>
        </table>
    </div>
    <nav aria-label="Page navigation">
        <ul class="pagination justify-content-center">
            <li class="page-item" :class="{ disabled: currentPage === 1 }">
                <a class="page-link" href="#" @click="previousPage">Previous</a>
            </li>
            <li class="page-item" v-for="page in totalPages" :key="page" :class="{ active: currentPage === page }">
                <a class="page-link" href="#" @click="changePage(page)">{{ page }}</a>
            </li>
            <li class="page-item" :class="{ disabled: currentPage === totalPages || addresses.length === 0 }">
                <a class="page-link" href="#" @click="nextPage">Next</a>
            </li>
        </ul>
    </nav>
</div>
</template>

<script>
export default {
    name: 'HistoryPage',
    props: {
        addresses: {
            type: Array,
            default: () => []
        }
    },
    data() {
        return {
            currentPage: 1,
            recordsPerPage: 10,
            selectedRecords: []
        }
    },
    computed: {
        // Calculates the number of paginated addresses per record page
        paginatedAddresses() {
            const startIndex = (this.currentPage - 1) * this.recordsPerPage
            const endIndex = startIndex + this.recordsPerPage;
            return this.addresses.slice(startIndex, endIndex)
        },

        // Calculates the total number of pages based on the number of available addresses divided by the recordsPerPage variable
        totalPages() {
            return Math.ceil(this.addresses.length / this.recordsPerPage)
        }
    },
    methods: {
        // Called when a user clicks on a non-active page number; calculates which page should be active instead
        changePage(page) {
            if (page >= 1 && page <= this.totalPages) {
                this.currentPage = page;
            }
        },

        // Called when a user clicks on the "Previous" button
        previousPage() {
            if (this.currentPage > 1) {
                this.currentPage--
            }
        },

        // Called when a user clicks on the "Next" button
        nextPage() {
            if (this.currentPage < this.totalPages) {
                this.currentPage++
            }
        },

        // Called when a user presses the "Delete" button, emits an event with the indexes of the selected records to be deleted and resets the selectedRecords array
        deleteSelectedRecords() {
            this.$emit('indexes-to-delete', this.selectedRecords)
            // Move to the previous page if current page is empty after deletion
            const newTotalPages = Math.ceil((this.addresses.length - this.selectedRecords.length) / this.recordsPerPage);
            if(newTotalPages < this.totalPages){
                this.previousPage()
            }
            this.selectedRecords = []
        }
    }
}
</script>

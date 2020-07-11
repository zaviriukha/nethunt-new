<template>
    <v-card class="ma-5">
        <v-data-table
                :headers="headers"
                :items="desserts"
                :items-per-page="itemsPerPage"
                sort-by="calories"
                class="elevation-1"
        >
            <template v-slot:top>
                <v-toolbar flat color="white">
                    <v-toolbar-title>USER LIST</v-toolbar-title>
                    <v-divider
                            class="mx-4"
                            inset
                            vertical
                    ></v-divider>
                    <v-spacer></v-spacer>
                    <v-dialog v-model="dialog" max-width="500px">
                        <template v-slot:activator="{ on, attrs }">
                            <v-btn
                                    color="primary"
                                    dark
                                    class="mb-2"
                                    v-bind="attrs"
                                    v-on="on"
                            >New Item
                            </v-btn>
                        </template>
                        <v-card>
                            <v-card-title>
                                <span class="headline">{{ formTitle }}</span>
                            </v-card-title>

                            <ValidationObserver ref="observer" v-slot="{ validate }">
                                <form>
                                    <v-card-text>
                                        <v-container>
                                            <v-row>
                                                <v-col cols="12" sm="6" md="4">
                                                    <ValidationProvider v-slot="{ errors, valid }" name="Name"
                                                                        rules="required|alpha_num"
                                                    >
                                                        <v-text-field
                                                                v-model="editedItem.name"
                                                                label="User name"
                                                                :error-messages="errors"
                                                                required
                                                        ></v-text-field>
                                                    </ValidationProvider>
                                                </v-col>
                                                <v-col cols="12" sm="6" md="4">
                                                    <ValidationProvider v-slot="{ errors, valid }" name="email"
                                                                        rules="required|email">
                                                        <v-text-field
                                                                v-model="editedItem.email"
                                                                label="Email"
                                                                :error-messages="errors"
                                                                required
                                                        ></v-text-field>
                                                    </ValidationProvider>
                                                </v-col>
                                                <v-col cols="12" sm="6" md="4">
                                                    <ValidationProvider v-slot="{ errors, valid }" name="numeric"
                                                                        rules="required|numeric">
                                                        <v-text-field
                                                                v-model="editedItem.number"
                                                                label="Phone number"
                                                                :error-messages="errors"
                                                                :counter="7"
                                                                required
                                                        ></v-text-field>
                                                    </ValidationProvider>
                                                </v-col>
                                                <v-col cols="12" sm="6" md="4">
                                                    <ValidationProvider v-slot="{ errors }" rules="required">
                                                        <v-menu
                                                                ref="menu"
                                                                v-model="menu"
                                                                :close-on-content-click="false"
                                                                :return-value.sync="editedItem.birthday"
                                                                transition="scale-transition"
                                                                offset-y
                                                                min-width="290px"
                                                        >
                                                            <template v-slot:activator="{ on, attrs }">
                                                                <v-text-field
                                                                        v-model="editedItem.birthday"
                                                                        label="Birthday"
                                                                        readonly
                                                                        v-bind="attrs"
                                                                        v-on="on"
                                                                        required
                                                                ></v-text-field>
                                                            </template>
                                                            <v-date-picker v-model="editedItem.birthday" no-title
                                                                           scrollable>
                                                                <v-spacer></v-spacer>
                                                                <v-btn text color="primary" @click="menu = false">Cancel
                                                                </v-btn>
                                                                <v-btn text color="primary"
                                                                       @click="$refs.menu.save(editedItem.birthday)">OK
                                                                </v-btn>
                                                            </v-date-picker>
                                                        </v-menu>
                                                    </ValidationProvider>
                                                </v-col>
                                            </v-row>
                                        </v-container>
                                    </v-card-text>

                                    <v-card-actions>
                                        <v-spacer></v-spacer>
                                        <v-btn color="blue darken-1" text @click="close">Cancel</v-btn>
                                        <v-btn color="blue darken-1" text @click="save">Save</v-btn>
                                    </v-card-actions>
                                </form>
                            </ValidationObserver>
                        </v-card>
                    </v-dialog>
                </v-toolbar>
            </template>
            <template v-slot:item.actions="{ item }">
                <v-icon
                        small
                        class="mr-2"
                        @click="editItem(item)"
                >
                    mdi-pencil
                </v-icon>
                <v-icon
                        small
                        @click="deleteItem(item)"
                >
                    mdi-delete
                </v-icon>
            </template>
            <template v-slot:no-data>
                <v-btn color="primary" @click="initialize">Reset</v-btn>
            </template>
        </v-data-table>
    </v-card>
</template>

<script>
    import {required, email, max, numeric, alpha_num} from 'vee-validate/dist/rules'
    import {extend, ValidationObserver, ValidationProvider, setInteractionMode} from 'vee-validate'

    setInteractionMode('eager')
    extend('required', {
        ...required,
        message: '{_field_} can not be empty',
    })
    extend('max', {
        ...max,
        message: '{_field_} may not be greater than {length} characters',
    })
    extend('email', {
        ...email,
        message: 'Email must be valid',
    })
    extend('numeric', {
        ...numeric,
        message: 'Use only numbers',
    })
    extend('alpha_num', {
        ...alpha_num,
        message: 'Use only numbers',
    })

    export default {
        name: "netHuntTable",
        components: {
            ValidationProvider, ValidationObserver,
        },
        data: () => ({
            dialog: false,
            itemsPerPage: 5,
            headers: [
                {
                    text: 'Name',
                    align: 'start',
                    sortable: false,
                    value: 'name',
                },
                {text: 'Email', value: 'email'},
                {text: 'Phone number', value: 'number'},
                {text: 'Birthday', value: 'birthday'},
                {text: 'Actions', value: 'actions', sortable: false},
            ],
            desserts: [],
            editedIndex: -1,
            editedItem: {
                name: '',
                email: 0,
                number: 0,
                birthday: new Date().toISOString().substr(0, 10),
            },
            defaultItem: {
                name: '',
                email: 0,
                number: 0,
                birthday: new Date().toISOString().substr(0, 10),
            },
            menu: false,
        }),
        computed: {
            formTitle() {
                return this.editedIndex === -1 ? 'New User' : 'Edit User'
            },
        },
        watch: {
            dialog(val) {
                val || this.close()
            },
        },
        created() {
            this.initialize()
        },
        methods: {
            initialize() {
                this.desserts = [
                    {
                        name: 'Maje Shallcroff',
                        email: 'llerego5@github.com',
                        number: '330-635-9643',
                        birthday: '1995-12-13',
                    },
                    {
                        name: 'Gwynne Townson',
                        email: 'rsommerlie8@ifeng.com',
                        number: '201-198-4324',
                        birthday: '1991-02-13',
                    },
                    {
                        name: 'Allix Blincow',
                        email: 'kchristopheb@msu.edu',
                        number: '750-375-6740',
                        birthday: '1999-05-31',
                    },
                    {
                        name: 'Charlie Jose',
                        email: 'frannf@goodreads.com',
                        number: '417-809-8082',
                        birthday: '1996-11-17',
                    },
                    {
                        name: 'Kermit Christophe',
                        email: 'lrawsthornej@oaic.gov.au',
                        number: '737-689-8624',
                        birthday: '1995-11-13',
                    },
                    {
                        name: 'Wilmar Derry',
                        email: 'abiasiolim@examiner.com',
                        number: '617-985-6513',
                        birthday: '1996-08-10',
                    },
                    {
                        name: 'Joey Toth',
                        email: 'plindborgo@tumblr.com',
                        number: '432-395-7035',
                        birthday: '1998-06-21',
                    },
                    {
                        name: 'Rodney Yegorkov',
                        email: 'vdenyukind@loc.gov',
                        number: '404-712-4200',
                        birthday: '1993-11-11',
                    },
                    {
                        name: 'Say Leynagh',
                        email: 'kchristopheb@msu.edu',
                        number: '471-273-1172',
                        birthday: '1992-10-27',
                    },
                    {
                        name: 'Denney Boldt',
                        email: 'cjose9@imgur.com',
                        number: '388-848-5375',
                        birthday: '1996-10-17',
                    },
                ]
            },
            editItem(item) {
                this.editedIndex = this.desserts.indexOf(item)
                this.editedItem = Object.assign({}, item)
                this.dialog = true
            },
            deleteItem(item) {
                const index = this.desserts.indexOf(item)
                confirm('Are you sure you want to delete this item?') && this.desserts.splice(index, 1)
            },
            clear() {
                this.name = ''
                this.email = ''
                this.number = ''
                this.birthday = ''
                this.$refs.observer.reset()
            },
            close() {
                this.dialog = false;
                this.clear();
                this.$nextTick(() => {
                    this.editedItem = Object.assign({}, this.defaultItem)
                    this.editedIndex = -1
                })
            },
            valid() {
                this.$refs.observer.validate().then(valid=> {
                    if (valid) {
                        debugger
                        console.log('good valid')
                        this.dialog = false
                        //this.close();
                    } else {
                        debugger
                        console.log('wrong valid')
                        this.dialog = true
                    }
                })
                // if (this.$refs.observer.validate() === false) {
                //     // debugger
                //     console.log('wrong valid')
                //     //this.dialog = true
                // } else {
                //     debugger
                //     console.log('shit')
                //     //this.dialog = false
                // }
            },
            save() {
                if (this.editedIndex > -1) {
                    this.valid();
                    Object.assign(this.desserts[this.editedIndex], this.editedItem)
                } else {
                    this.valid();
                    this.desserts.push(this.editedItem);
                }
                //this.close()
            },
        },
    }
</script>

<style lang="scss">
    .v-data-table,
    .v-toolbar__content {
        background-color:  #00E5FF!important;
    }

    .theme--light.v-data-table > .v-data-table__wrapper > table > tbody > tr:hover:not(.v-data-table__expanded__content):not(.v-data-table__empty-wrapper) {
        background-color: #00BCD4 !important;
    }
</style>
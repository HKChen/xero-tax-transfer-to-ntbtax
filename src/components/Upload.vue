<template>
    <v-row>
        <v-col cols="3">
            <v-card-text>
                <v-file-input
                    truncate-length="200"
                    v-model="fileSelection"
                    label="稅籍及統編上傳"
                    outlined
                    dense
                    @change="importSelectionExcel(fileSelection)"
                ></v-file-input>
                <v-card>
                    <v-card-title>
                        稅籍統編
                        <v-spacer></v-spacer>
                        <v-text-field v-model="search" label="Search">
                        </v-text-field>
                        <!-- <v-btn color="primary" class="ma-2 white--text">
                            增加稅籍統編
                            <v-icon right dark>
                                mdi-plus-box-outline
                            </v-icon>
                        </v-btn> -->
                    </v-card-title>
                    <v-card-title>
                        <v-data-table
                            dense
                            :headers="selectionHeader"
                            :items="selection"
                            :search="search"
                            :height="600"
                            fixed-header
                            :footer-props="{
                                'items-per-page-options': [100, 100],
                            }"
                        >
                            <template v-slot:item="prop">
                                <tr :class="prop.item.Id === selectedRow ? 'background-color: yellow' : ''"
                                    @click="handleClick(prop.item)">
                                    <td class="text-center">
                                        {{ prop.item.Company }}
                                    </td>
                                    <td class="text-center">
                                        {{ prop.item.Taxation }}
                                    </td>
                                    <td class="text-center">
                                        {{ prop.item.TaxId }}
                                    </td>
                                </tr>
                            </template>
                        </v-data-table>
                    </v-card-title>
                </v-card>
            </v-card-text>
        </v-col>
        <v-col cols="9">
            <v-card-text>
                <v-file-input
                    truncate-length="200"
                    v-model="file"
                    label="欲轉檔之檔案上傳"
                    outlined
                    dense
                    @change="importExcel(file)"
                ></v-file-input>

                <v-card>
                    <v-card-title>
                        報稅碼
                        <v-spacer></v-spacer>
                        <v-btn
                            color="primary"
                            class="ma-2 white--text"
                            @click="downloadFile"
                        >
                            下載國稅局報稅轉檔
                            <v-icon right dark>
                                mdi-arrow-down-bold-box-outline
                            </v-icon>
                        </v-btn>
                    </v-card-title>
                    <v-card-title>
                        <v-data-table
                            dense
                            :headers="header"
                            :items="filterXLSXJson"
                            :server-items-length="filterXLSXJson.length"
                            :height="600"
                            item-key="itemId"
                            :options.sync="options"
                            :items-per-page="perpage"
                            fixed-header
                            :footer-props="{
                                'items-per-page-options': [100, 100],
                            }"
                        >
                            <template v-slot:item="prop">
                                <tr>
                                    <td class="text-right">
                                        {{ prop.item.itemId }}
                                    </td>
                                    <td class="text-center">
                                        {{ prop.item.receiptClass }}
                                    </td>
                                    <td class="text-center">
                                        <v-text-field
                                             v-bind="prop.item.taxation=taxtaionSelcted"
                                             :value="taxtaionSelcted"
                                        ></v-text-field>
                                    </td>
                                    <td class="text-center">
                                        {{ prop.item.no }}
                                    </td>
                                    <td class="text-center">
                                        {{ prop.item.yyyMM }}
                                    </td>
                                    <td class="text-center">
                                        <v-text-field
                                             v-bind="prop.item.taxId2=taxIdSelected"
                                             :value="taxIdSelected"
                                        ></v-text-field>
                                    </td>
                                    <td class="text-center">
                                        {{ prop.item.taxId }}
                                    </td>
                                    <td class="text-center">
                                        {{ prop.item.prefix }}
                                    </td>
                                    <td class="text-center">
                                        {{ prop.item.serialNo }}
                                    </td>
                                    <td class="text-center">
                                        {{ prop.item.untaxed }}
                                    </td>
                                    <td class="text-center">
                                        {{ prop.item.tax }}
                                    </td>
                                    <td class="text-center">
                                        {{ prop.item.taxed }}
                                    </td>
                                    <td class="text-center">
                                        {{ prop.item.taxCredit }}
                                    </td>
                                    <td class="text-center">
                                        {{ prop.item.space }}
                                    </td>
                                    <td>
                                        {{ prop.item | taxCode }}
                                    </td>
                                </tr>
                            </template>
                        </v-data-table>
                    </v-card-title>
                </v-card>
            </v-card-text>
        </v-col>
    </v-row>
</template>

<script>
import XLSX from "xlsx";

export default {
    name: "Upload",
    components: {
        // XLSX
    },
    data: () => ({
        search: '',
        selectedRow: null,
        file: [],
        fileSelection: [],
        xlsxJson: [],
        selectionXlsxJson: [],
        selection: [],
        taxtaionSelcted: '',
        taxIdSelected: '',
        finalData: [],
        selectionHeader: [
            {
                text: "公司名稱",
                align: "center",
                sortable: false,
                value: "Company",
            },
            {
                text: "稅籍",
                align: "center",
                sortable: false,
                value: "Taxation",
            },
            {
                text: "統編",
                align: "center",
                sortable: false,
                value: "TaxId",
            },
        ],
        header: [
            {
                text: "項次",
                align: "center",
                sortable: false,
                value: "itemId",
            },

            {
                text: "發票種類",
                align: "center",
                sortable: false,
                value: "receiptClass",
            },

            {
                text: "稅籍(可選)",
                align: "center",
                sortable: false,
                value: "taxation",
            },

            {
                text: "序號",
                align: "center",
                sortable: false,
                value: "no",
            },

            {
                text: "年月",
                align: "center",
                sortable: false,
                value: "yyyMM",
            },

            {
                text: "統編2(可選)",
                align: "center",
                sortable: false,
                value: "taxId2",
            },

            {
                text: "統編1",
                align: "center",
                sortable: false,
                value: "taxId",
            },

            {
                text: "字軌",
                align: "center",
                sortable: false,
                value: "prefix",
            },

            {
                text: "字號",
                align: "center",
                sortable: false,
                value: "serialNo",
            },

            {
                text: "金額",
                align: "center",
                sortable: false,
                value: "untaxed",
            },

            {
                text: "課稅",
                align: "center",
                sortable: false,
                value: "tax",
            },

            {
                text: "稅額",
                align: "center",
                sortable: false,
                value: "taxed",
            },

            {
                text: "扣抵",
                align: "center",
                sortable: false,
                value: "taxCredit",
            },

            {
                text: "空白",
                align: "center",
                sortable: false,
                value: "space",
            },

            {
                text: "報稅碼",
                align: "center",
                sortable: false,
                value: "",
            },
        ],
        selectionOptions: {
            page: 1,
            itemsPerPage: 100,
            pageStart: 1,
            pageStop: 1,
        },
        selectionPerpage: 100,
        options: {
            page: 1,
            itemsPerPage: 100,
            pageStart: 1,
            pageStop: 1,
        },
        perpage: 100,
    }),
    methods: {
        importSelectionExcel(file) {
            this.selectionXlsxJson = [];
            this.taxtaionSelcted = '';
            this.taxIdSelected = '';
            this.selection = [];
            this.selectedRow = null;
            const types = file.name.split(".")[1];
            const fileType = ["xlsx", "xls", "csv"].some(
                (item) => item === types
            );
            if (!fileType) {
                alert("upload type error, please select again.");
                return;
            }
            this.file2Xce(file).then((tabJson) => {
                if (tabJson && tabJson.length > 0) {
                    this.selectionXlsxJson = JSON.parse(
                        JSON.stringify(tabJson)
                    );
                    let count = 1;
                     this.selectionXlsxJson[0].sheet.forEach(item => {
                        this.selection.push({
                            Id: count,
                            Company: item.Company,
                            Taxation: item.Taxation,
                            TaxId: item.TaxId,
                        })
                        count++;
                    });
                }
            });
        },
        importExcel(file) {
            this.xlsxJson = [];
            this.finalData = [];
            const types = file.name.split(".")[1];
            const fileType = ["xlsx", "xls", "csv"].some(
                (item) => item === types
            );
            if (!fileType) {
                alert("upload type error, please select again.");
                return;
            }
            this.file2Xce(file).then((tabJson) => {
                if (tabJson && tabJson.length > 0) {
                    this.xlsxJson = JSON.parse(JSON.stringify(tabJson));
                }
            });
        },
        file2Xce(file) {
            return new Promise(function(resolve, reject) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    const data = e.target.result;
                    this.wb = XLSX.read(data, {
                        type: "binary",
                    });
                    const result = [];
                    this.wb.SheetNames.forEach((sheetName, index) => {
                        result.push({
                            // sheetName: sheetName,
                            sheet: XLSX.utils.sheet_to_json(
                                this.wb.Sheets[sheetName]
                            ),
                        });
                    });
                    resolve(result);
                };
                reader.readAsBinaryString(file); // 传统input方法
            });
        },
        handleClick(value) {
            this.selectedRow = value.Id;
            this.taxtaionSelcted = value.Taxation;
            this.taxIdSelected = value.TaxId;
        },
        downloadFile() {
            let content = "";
            this.finalData.forEach((item) => {
                content = content.concat(
                    item.receiptClass,
                    item.taxation,
                    item.no,
                    item.yyyMM,
                    item.taxId2,
                    item.taxId,
                    item.prefix,
                    item.serialNo,
                    item.untaxed,
                    item.tax,
                    item.taxed,
                    item.taxCredit,
                    item.space,
                    "\n"
                );
            });
            var element = document.createElement("a");
            element.setAttribute(
                "href",
                "data:text/plain;charset=utf-8," + encodeURIComponent(content)
            );
            element.setAttribute(
                "download",
                "申報檔" +
                    new Date()
                        .toISOString()
                        .replaceAll("-", "")
                        .replace("T", "")
                        .replaceAll(":", "")
                        .slice(0, 14) +
                    ".txt"
            );

            element.style.display = "none";
            document.body.appendChild(element);
            element.click();
            document.body.removeChild(element);
        },
    },
    computed: {
        filterXLSXJson() {
            if (this.xlsxJson.length > 0 && this.file !== null) {
                const data = [];
                this.xlsxJson[0].sheet.forEach((item) => {
                    if (
                        item.__EMPTY !== undefined &&
                        item.__EMPTY !== null &&
                        item.__EMPTY !== "Type"
                    ) {
                        data.push({
                            name: item.__EMPTY_1,
                            cost: item.__EMPTY_3,
                        });
                    }
                });

                let count = 1;
                data.forEach((item) => {
                    let ary = item.name.match(/(\w+)/gi);
                    this.finalData.push({
                        itemId: count.toString(), // 項次
                        no: count.toString().padStart(7, "0"),
                        prefix: ary[0],
                        serialNo: ary[1],
                        untaxed: ary[2].padStart(12, "0"),
                        taxed: ary[3].padStart(10, "0"),
                        taxId: ary[4],
                        yyyMM: ary[5],
                        receiptClass: ary[6],
                        taxation: "",
                        taxId2: "",
                        tax: 1,
                        taxCredit: 1,
                        space: "        ", // format need to 8 space char
                    });
                    count++;
                });

                return this.finalData;
            } else {
                return this.xlsxJson;
            }
        },
    },
    filters: {
        taxCode(value) {
            let result = value.receiptClass.concat(
                value.taxation,
                value.no,
                value.yyyMM,
                value.taxId2,
                value.taxId,
                value.prefix,
                value.serialNo,
                value.untaxed,
                value.tax,
                value.taxed,
                value.taxCredit,
                value.space
            );

            return result;
        },
    },
};
</script>

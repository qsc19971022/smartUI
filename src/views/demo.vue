<template>
  <div className="vue-root">
    <smart-grid
      id="grid"
      ref="grid"
      @change="change"
      @onCloseColumnDialog="onCloseColumnDialog"
      @endEdit="onRowEndEdit"
    ></smart-grid>
  </div>
</template>

<script setup>
import { onMounted, ref } from "vue";
import "smart-webcomponents/source/styles/smart.default.css";
import "smart-webcomponents/source/modules/smart.grid.js";
onMounted(() => {
  const moodTemplate = document.createElement("template");
  moodTemplate.id = "moodTemplate";
  moodTemplate.innerHTML = `<div>Mood: {{value=😊}}</div>`;
  document.body.appendChild(moodTemplate);

  const moodEditorTemplate = document.createElement("template");
  moodEditorTemplate.id = "moodEditorTemplate";
  moodEditorTemplate.innerHTML = ` <div tabindex="0" style="padding: 0px; display:flex; justify-content: center;">
        <span tabindex="1">😊</span>
        <span tabindex="2">😐</span>
        <span tabindex="3">😂</span>
      </div>`;
  document.body.appendChild(moodEditorTemplate);
  window.Smart(
    "#grid",
    class {
      get properties() {
        return {
          dataSource: new window.Smart.DataAdapter({
            dataSource: [
              {
                EmployeeID: 1,
                FirstName: "Nancy",
                LastName: "Davolio",
                ReportsTo: 2,
                Country: "USA",
                Title: "Sales Representative",
                HireDate: "1992-05-01 00:00:00",
                BirthDate: "1948-12-08 00:00:00",
                City: "Seattle",
                Address: "507 - 20th Ave. E.Apt. 2A",
              },
              {
                EmployeeID: 2,
                FirstName: "Andrew",
                LastName: "Fuller",
                ReportsTo: null,
                Country: "USA",
                Title: "Vice President, Sales",
                HireDate: "1992-08-14 00:00:00",
                BirthDate: "1952-02-19 00:00:00",
                City: "Tacoma",
                Address: "908 W. Capital Way",
              },
              {
                EmployeeID: 3,
                FirstName: "Janet",
                LastName: "Leverling",
                ReportsTo: 2,
                Country: "USA",
                Title: "Sales Representative",
                HireDate: "1992-04-01 00:00:00",
                BirthDate: "1963-08-30 00:00:00",
                City: "Kirkland",
                Address: "722 Moss Bay Blvd.",
              },
              {
                EmployeeID: 4,
                FirstName: "Margaret",
                LastName: "Peacock",
                ReportsTo: 2,
                Country: "USA",
                Title: "Sales Representative",
                HireDate: "1993-05-03 00:00:00",
                BirthDate: "1937-09-19 00:00:00",
                City: "Redmond",
                Address: "4110 Old Redmond Rd.",
              },
              {
                EmployeeID: 5,
                FirstName: "Steven",
                LastName: "Buchanan",
                ReportsTo: 2,
                Country: "UK",
                Title: "Sales Manager",
                HireDate: "1993-10-17 00:00:00",
                BirthDate: "1955-03-04 00:00:00",
                City: "London",
                Address: "14 Garrett Hill",
              },
              {
                EmployeeID: 6,
                FirstName: "Michael",
                LastName: "Suyama",
                ReportsTo: 5,
                Country: "UK",
                Title: "Sales Representative",
                HireDate: "1993-10-17 00:00:00",
                BirthDate: "1963-07-02 00:00:00",
                City: "London",
                Address: "Coventry House Miner Rd.",
              },
              {
                EmployeeID: 7,
                FirstName: "Robert",
                LastName: "King",
                ReportsTo: 5,
                Country: "UK",
                Title: "Sales Representative",
                HireDate: "1994-01-02 00:00:00",
                BirthDate: "1960-05-29 00:00:00",
                City: "London",
                Address: "Edgeham Hollow Winchester Way",
              },
              {
                EmployeeID: 8,
                FirstName: "Laura",
                LastName: "Callahan",
                ReportsTo: 2,
                Country: "USA",
                Title: "Inside Sales Coordinator",
                HireDate: "1994-03-05 00:00:00",
                BirthDate: "1958-01-09 00:00:00",
                City: "Seattle",
                Address: "4726 - 11th Ave. N.E.",
              },
              {
                EmployeeID: 9,
                FirstName: "Anne",
                LastName: "Dodsworth",
                ReportsTo: 5,
                Country: "UK",
                Title: "Sales Representative",
                HireDate: "1994-11-15 00:00:00",
                BirthDate: "1966-01-27 00:00:00",
                City: "London",
                Address: "7 Houndstooth Rd.",
              },
            ],
            id: "EmployeeID",
            dataFields: [
              "EmployeeID: number",
              "ReportsTo: number",
              "FirstName: string",
              "LastName: string",
              "Country: string",
              "City: string",
              "Address: string",
              "Title: string",
              "HireDate: date",
              "BirthDate: date",
            ],
          }),
          appearance: {
            autoShowColumnFilterButton: false,
          },
          editing: {
            enabled: true,
            action: "none",
            mode: "row",
            commandColumn: {
              visible: true,
            },
            dialog: {
              enabled: true,
            },
          },
          sorting: {
            enabled: true,
            mode: "one",
          },
          selection: {
            enabled: true,
            checkBoxes: {
              enabled: true,
            },
            allowRowSelection: false,
          },
          filtering: {
            enabled: true,
            filterMenu: {
              // mode: "excel",
              buttons: ["筛选", "啦啦"],
            },
          },
          keyboardNavigation: true,
          columns: [
            {
              label: "First Name",
              dataField: "FirstName",
              columnGroup: "name",
              width: 200,
            },
            {
              label: "Last Name",
              dataField: "LastName",
              columnGroup: "name",
              editor: {
                template: "#moodEditorTemplate",
                onInit(index, dataField, editor) {
                  editor.addEventListener("click", (event) => {
                    console.log(event);
                    editor.firstElementChild.value = event.target.innerHTML;
                  });
                },
              },
              template: "#moodTemplate",
              width: 200,
            },
            { label: "Title", dataField: "Title", width: 160 },
            {
              label: "Birth Date",
              dataField: "BirthDate",
              cellsFormat: "d",
              width: 120,
            },
            {
              label: "Hire Date",
              dataField: "HireDate",
              cellsFormat: "d",
            },
            { label: "Address", dataField: "Address" },
            { label: "City", dataField: "City" },
            { label: "Country", dataField: "Country" },
          ],
        };
      }
    }
  );
});
const onRowEndEdit = (event) => {
  console.log(event);
};
const grid = ref(null);
const change = (event, obj) => {
  console.log(event, obj);
  // console.log(grid.value.getSelection());
};
const onCloseColumnDialog = (event) => {
  console.log(event);
};
</script>

<style>
smart-grid {
  width: 100%;
  height: auto;
}
</style>

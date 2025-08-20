import React, { useState } from 'react';
import { Card } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import jsPDF from "jspdf";
import autoTable from "jspdf-autotable";
import * as XLSX from "xlsx";

const canteenMenu = {
  "Small Lunch": 450,
  "Large Lunch": 500,
  "Small Soup": 200,
  "Medium Soup": 250,
  "Large Soup": 300,
  "Small Chicken & Chips": 500,
  "Large Chicken & Chips": 550,
  "Path Medium Soup": 0,
  "Path Small Lunch": 0,
};

const tuckShopMenu = {
  "Bun & Cheese": 300,
  "Cocoa Bread": 200,
  "Hot Dog": 200,
  "Beef Patty": 300,
  "Chicken Patty": 350,
};

export default function LunchBrowser() {
  const [orders, setOrders] = useState([]);
  const [form, setForm] = useState({
    teacher: "",
    class: "",
    date: new Date().toISOString().split("T")[0],
    canteen: {},
    tuckShop: {},
  });

  const handleInput = (type, item, value) => {
    setForm({ ...form, [type]: { ...form[type], [item]: parseInt(value) || 0 } });
  };

  const calculateTotal = (items, menu) => Object.entries(items).reduce((acc, [k, v]) => acc + (menu[k] * v), 0);

  const addOrder = () => {
    setOrders([...orders, form]);
    setForm({ teacher: "", class: "", date: new Date().toISOString().split("T")[0], canteen: {}, tuckShop: {} });
  };

  const deleteOrder = (index) => setOrders(orders.filter((_, i) => i !== index));

  const groupByClass = () => {
    const grouped = {};
    orders.forEach(order => {
      if (!grouped[order.class]) grouped[order.class] = [];
      grouped[order.class].push(order);
    });
    return grouped;
  };

  const exportPDF = () => {
    const doc = new jsPDF();
    doc.text(`Servite Lunch Orders Report`, 14, 15);

    let y = 20;
    orders.forEach(order => {
      doc.text(`Class: ${order.class} | Teacher: ${order.teacher} | Date: ${order.date}`, 14, y);
      y += 6;
      doc.text(`Canteen Items:`, 14, y);
      y += 6;
      Object.entries(order.canteen).forEach(([item, qty]) => {
        if (qty > 0) {
          doc.text(`${item} x${qty} = $${canteenMenu[item]*qty}`, 20, y);
          y += 6;
        }
      });
      doc.text(`Tuck Shop Items:`, 14, y);
      y += 6;
      Object.entries(order.tuckShop).forEach(([item, qty]) => {
        if (qty > 0) {
          doc.text(`${item} x${qty} = $${tuckShopMenu[item]*qty}`, 20, y);
          y += 6;
        }
      });
      const canteenTotal = calculateTotal(order.canteen, canteenMenu);
      const tuckShopTotal = calculateTotal(order.tuckShop, tuckShopMenu);
      doc.text(`Canteen Total: $${canteenTotal} | Tuck Shop Total: $${tuckShopTotal} | Class Total: $${canteenTotal+tuckShopTotal}`, 14, y);
      y += 10;
    });

    doc.save(`Servite_Lunch_Report_${new Date().toISOString().split('T')[0]}.pdf`);
  };

  const exportExcel = () => {
    const wb = XLSX.utils.book_new();
    const wsData = [];

    orders.forEach(order => {
      Object.entries(order.canteen).forEach(([item, qty]) => {
        if (qty > 0) wsData.push([order.class, order.teacher, order.date, item, qty, 'Canteen', canteenMenu[item]*qty]);
      });
      Object.entries(order.tuckShop).forEach(([item, qty]) => {
        if (qty > 0) wsData.push([order.class, order.teacher, order.date, item, qty, 'Tuck Shop', tuckShopMenu[item]*qty]);
      });
    });

    const ws = XLSX.utils.aoa_to_sheet([['Class','Teacher','Date','Item','Quantity','Type','Total'], ...wsData]);
    XLSX.utils.book_append_sheet(wb, ws, 'Orders');
    XLSX.writeFile(wb, `Servite_Lunch_Report_${new Date().toISOString().split('T')[0]}.xlsx`);
  };

  const groupedOrders = groupByClass();

  return (
    <div className="p-6 space-y-6">
      <Card className="p-4">
        <h2 className="text-xl font-bold mb-2">📘 How to Use the Lunch Browser</h2>
        <ol className="list-decimal list-inside space-y-1 text-sm">
          <li>Enter the <b>Teacher Name</b> and <b>Class</b>.</li>
          <li>Select the <b>Date</b> for the lunch order.</li>
          <li>Input the quantity for each <b>Canteen</b> and <b>Tuck Shop</b> item.</li>
          <li>Click <b>Add Order</b> to save the order.</li>
          <li>Orders will appear in the <b>Orders Report</b> section, grouped by class.</li>
          <li>Click <b>Export PDF</b> or <b>Export Excel</b> to download all orders.</li>
          <li>Click <b>Print Canteen Sheet</b> or <b>Print Tuck Shop Sheet</b> to generate kitchen prep sheets.</li>
          <li>To remove an order, click the <b>Delete</b> button next to it.</li>
        </ol>
      </Card>

      <Card className="p-4">
        <h2 className="text-xl font-bold mb-2">New Lunch Order</h2>
        <div className="grid grid-cols-3 gap-4">
          <input className="border p-2" placeholder="Teacher Name" value={form.teacher} onChange={(e) => setForm({ ...form, teacher: e.target.value })} />
          <input className="border p-2" placeholder="Class" value={form.class} onChange={(e) => setForm({ ...form, class: e.target.value })} />
          <input type="date" className="border p-2" value={form.date} onChange={(e) => setForm({ ...form, date: e.target.value })} />
        </div>

        <h3 className="mt-4 font-semibold">Canteen Menu</h3>
        <div className="grid grid-cols-2 gap-2">
          {Object.entries(canteenMenu).map(([item, price]) => (
            <div key={item} className="flex justify-between items-center">
              <label>{item} (${price})</label>
              <input type="number" className="w-16 border p-1" min="0" value={form.canteen[item] || 0} onChange={(e) => handleInput("canteen", item, e.target.value)} />
            </div>
          ))}
        </div>

        <h3 className="mt-4 font-semibold">Tuck Shop Menu</h3>
        <div className="grid grid-cols-2 gap-2">
          {Object.entries(tuckShopMenu).map(([item, price]) => (
            <div key={item} className="flex justify-between items-center">
              <label>{item} (${price})</label>
              <input type="number" className="w-16 border p-1" min="0" value={form.tuckShop[item] || 0} onChange={(e) => handleInput("tuckShop", item, e.target.value)} />
            </div>
          ))}
        </div>

        <Button className="mt-4" onClick={addOrder}>Add Order</Button>
      </Card>

      <Card className="p-4 flex gap-2">
        <Button onClick={() => exportPDF()}>Export PDF</Button>
        <Button onClick={() => exportExcel()}>Export Excel</Button>
      </Card>

      <Card className="p-4">
        <h2 className="text-xl font-bold mb-2">Orders Report</h2>
        {Object.entries(groupedOrders).map(([cls, clsOrders]) => (
          <div key={cls} className="border p-2 mb-4 rounded">
            <h3 className="font-semibold">Class: {cls}</h3>
            {clsOrders.map((order, i) => {
              const canteenTotal = calculateTotal(order.canteen, canteenMenu);
              const tuckShopTotal = calculateTotal(order.tuckShop, tuckShopMenu);
              return (
                <div key={i} className="border p-2 mt-2 rounded">
                  <p><b>Teacher:</b> {order.teacher} | <b>Date:</b> {order.date}</p>

                  <div className="mt-2">
                    <p className="font-semibold">Canteen Items:</p>
                    <ul className="list-disc list-inside">
                      {Object.entries(order.canteen).map(([item, qty]) => qty > 0 && (<li key={item}>{item} x{qty} = ${canteenMenu[item] * qty}</li>))}
                    </ul>
                  </div>

                  <div className="mt-2">
                    <p className="font-semibold">Tuck Shop Items:</p>
                    <ul className="list-disc list-inside">
                      {Object.entries(order.tuckShop).map(([item, qty]) => qty > 0 && (<li key={item}>{item} x{qty} = ${tuckShopMenu[item] * qty}</li>))}
                    </ul>
                  </div>

                  <p className="mt-2">Canteen Total: ${canteenTotal} | Tuck Shop Total: ${tuckShopTotal} | Class Grand Total: ${canteenTotal + tuckShopTotal}</p>
                  <Button className="mt-1" variant="destructive" onClick={() => deleteOrder(i)}>Delete</Button>
                </div>
              );
            })}
          </div>
        ))}
      </Card>
    </div>
  );
}
